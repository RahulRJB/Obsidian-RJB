
# Prompt caching


DATE:  20-09-24


Tags: [[Notes/LLMs|LLMs]]

# References:

https://eugeneyan.com/writing/llm-patterns/#caching-to-reduce-latency-and-cost


# Content:

### Why caching?

Caching can significantly reduce latency for responses that have been served before. In addition, by eliminating the need to compute a response for the same input again and again, we can reduce the number of LLM requests and thus save cost. Also, there are certain use cases that do not support latency on the order of seconds. Thus, pre-computing and caching may be the only way to serve those use cases.


When a new request is received:

- Embedding generator: This embeds the request via various models such as OpenAI’s `text-embedding-ada-002`, FastText, Sentence Transformers, and more.
- Similarity evaluator: This computes the similarity of the request via the vector store and then provides a distance metric. The vector store can either be local (FAISS, Hnswlib) or cloud-based. It can also compute similarity via a model.
- Cache storage: If the request is similar, the cached response is fetched and served.
- LLM: If the request isn’t similar enough, it gets passed to the LLM which then generates the result. Finally, the response is served and cached for future use.

### How to apply caching?

**We need to understand user request patterns**. This allows us to design the cache thoughtfully so it can be applied reliably.

Now, bringing it back to LLM responses. Imagine we get a request for a summary of “Mission Impossible 2” that’s semantically similar enough to “Mission Impossible 3”. If we’re looking up cache based on semantic similarity, we could serve the wrong response.

We also need to **consider if caching is effective for the usage pattern.** One way to quantify this is via the **cache hit rate** (percentage of requests served directly from the cache). If the usage pattern is uniformly random, the cache would need frequent updates. Thus, the effort to keep the cache up-to-date could negate any benefit a cache has to offer. On the other hand, if the usage follows a power law where a small proportion of unique requests account for the majority of traffic (e.g., search queries, product views), then caching could be an effective strategy.

Beyond semantic similarity, we could also explore caching based on:

- **Item IDs:** This applies when we pre-compute [summaries of product reviews](https://www.cnbc.com/2023/06/12/amazon-is-using-generative-ai-to-summarize-product-reviews.html) or generate a summary for an entire movie trilogy.
- **Pairs of Item IDs:** Such as when we generate comparisons between two movies. While this appears to be O(N2), in practice, a small number of combinations drive the bulk of traffic, such as comparison between popular movies in a series or genre.
- **Constrained input:** Such as variables like movie genre, director, or lead actor. For example, if a user is looking for movies by a specific director, we could execute a structured query and run it through an LLM to frame the response more eloquently. Another example is [generating code based on drop-down options](https://cheatlayer.com/)—if the code has been verified to work, we can cache it for reliable reuse.

Also, **caching doesn’t only have to occur on-the-fly.** As Redis shared, we can pre-compute LLM generations offline or asynchronously before serving them. By serving from a cache, we shift the latency from generation (typically seconds) to cache lookup (milliseconds). Pre-computing in batch can also help reduce cost relative to serving in real-time.

While the approaches listed here may not be as flexible as semantically caching on natural language inputs, I think it provides a good balance between efficiency and reliability.

## Guardrails: To ensure output quality[](https://eugeneyan.com/writing/llm-patterns/#guardrails-to-ensure-output-quality)

In the context of LLMs, guardrails validate the output of LLMs, ensuring that the output doesn’t just sound good but is also syntactically correct, factual, and free from harmful content. It also includes guarding against adversarial input.

### Why guardrails?[](https://eugeneyan.com/writing/llm-patterns/#why-guardrails)

First, they help ensure that model outputs are reliable and consistent enough to use in production. For example, we may require output to be in a specific JSON schema so that it’s machine-readable, or we need code generated to be executable. Guardrails can help with such syntactic validation.

Second, they provide an additional layer of safety and maintain quality control over an LLM’s output. For example, to verify if the content generated is appropriate for serving, we may want to check that the output isn’t harmful, verify it for factual accuracy, or ensure coherence with the context provided.

### More about guardrails[](https://eugeneyan.com/writing/llm-patterns/#more-about-guardrails)

**One approach is to control the model’s responses via prompts.** For example, Anthropic shared about prompts designed to guide the model toward generating responses that are [helpful, harmless, and honest](https://arxiv.org/abs/2204.05862) (HHH). They found that Python fine-tuning with the HHH prompt led to better performance compared to fine-tuning with RLHF.

![Example of HHH prompt](https://eugeneyan.com/assets/hhh.jpg "Example of HHH prompt")

Example of HHH prompt ([source](https://arxiv.org/abs/2204.05862))

**A more common approach is to validate the output.** An example is the [Guardrails package](https://github.com/ShreyaR/guardrails). It allows users to add structural, type, and quality requirements on LLM outputs via Pydantic-style validation. And if the check fails, it can trigger corrective action such as filtering on the offending output or regenerating another response.

Most of the validation logic is in [`validators.py`](https://github.com/ShreyaR/guardrails/blob/main/guardrails/validators.py). It’s interesting to see how they’re implemented. Broadly speaking, its validators fall into the following categories:

- Single output value validation: This includes ensuring that the output (i) is one of the predefined choices, (ii) has a length within a certain range, (iii) if numeric, falls within an expected range, and (iv) is a complete sentence.
- Syntactic checks: This includes ensuring that generated URLs are valid and reachable, and that Python and SQL code is bug-free.
- Semantic checks: This verifies that the output is aligned with the reference document, or that the extractive summary closely matches the source document. These checks can be done via cosine similarity or fuzzy matching techniques.
- Safety checks: This ensures that the generated output is free of inappropriate language or that the quality of translated text is high.

Nvidia’s [NeMo-Guardrails](https://github.com/NVIDIA/NeMo-Guardrails) follows a similar principle but is designed to guide LLM-based conversational systems. Rather than focusing on syntactic guardrails, it emphasizes semantic ones. This includes ensuring that the assistant steers clear of politically charged topics, provides factually correct information, and can detect jailbreaking attempts.

Thus, NeMo’s approach is somewhat different: Instead of using more deterministic checks like verifying if a value exists in a list or inspecting code for syntax errors, NeMo leans heavily on using another LLM to validate outputs (inspired by [SelfCheckGPT](https://arxiv.org/abs/2303.08896)).

In their example for fact-checking and preventing hallucination, they ask the LLM itself to check whether the most recent output is consistent with the given context. To fact-check, the LLM is queried if the response is true based on the documents retrieved from the knowledge base. To prevent hallucinations, since there isn’t a knowledge base available, they get the LLM to generate multiple alternative completions which serve as the context. The underlying assumption is that if the LLM produces multiple completions that disagree with one another, the original completion is likely a hallucination.

The moderation example follows a similar approach: The response is screened for harmful and unethical content via an LLM. Given the nuance of ethics and harmful content, heuristics and conventional machine learning techniques fall short. Thus, an LLM is required for a deeper understanding of the intent and structure of dialogue.

Apart from using guardrails to verify the output of LLMs, we can also **directly steer the output to adhere to a specific grammar.** An example of this is Microsoft’s [Guidance](https://github.com/microsoft/guidance). Unlike Guardrails which [imposes JSON schema via a prompt](https://github.com/ShreyaR/guardrails/blob/main/guardrails/constants.xml#L14), Guidance enforces the schema by injecting tokens that make up the structure.

We can think of Guidance as a domain-specific language for LLM interactions and output. It draws inspiration from [Handlebars](https://handlebarsjs.com/), a popular templating language used in web applications that empowers users to perform variable interpolation and logical control.

However, Guidance sets itself apart from regular templating languages by executing linearly. This means it maintains the order of tokens generated. Thus, by inserting tokens that are part of the structure—instead of relying on the LLM to generate them correctly—Guidance can dictate the specific output format. In their examples, they show how to [generate JSON that’s always valid](https://github.com/microsoft/guidance#guaranteeing-valid-syntax-json-example-notebook), [generate complex output formats](https://github.com/microsoft/guidance#rich-output-structure-example-notebook) with multiple keys, ensure that LLMs [play the right roles](https://github.com/microsoft/guidance#role-based-chat-model-example-notebook), and have [agents interact with each other](https://github.com/microsoft/guidance#agents-notebook).

They also introduced a concept called [token healing](https://github.com/microsoft/guidance#token-healing-notebook), a useful feature that helps avoid subtle bugs that occur due to tokenization. In simple terms, it rewinds the generation by one token before the end of the prompt and then restricts the first generated token to have a prefix matching the last token in the prompt. This eliminates the need to fret about token boundaries when crafting prompts.

### How to apply guardrails?[](https://eugeneyan.com/writing/llm-patterns/#how-to-apply-guardrails)

Though the concept of guardrails for LLMs in industry is still nascent, there are a handful of immediately useful and practical strategies we can consider.

**Structural guidance:** Apply guidance whenever possible. It provides direct control over outputs and offers a more precise method to ensure that output conforms to a specific structure or format.

**Syntactic guardrails:** These include checking if categorical output is within a set of acceptable choices, or if numeric output is within an expected range. Also, if we generate SQL, these can verify its free from syntax errors and also ensure that all columns in the query match the schema. Ditto for generating code (e.g., Python, JavaScript).

**Content safety guardrails:** These verify that the output has no harmful or inappropriate content. It can be as simple as checking against the [List of Dirty, Naughty, Obscene, and Otherwise Bad Words](https://github.com/LDNOOBW/List-of-Dirty-Naughty-Obscene-and-Otherwise-Bad-Words) or using [profanity detection](https://pypi.org/project/profanity-check/) models. (It’s [common to run moderation classifiers on output](https://twitter.com/goodside/status/1685023251532320768).) More complex and nuanced output can rely on an LLM evaluator.

**Semantic/factuality guardrails:** These confirm that the output is semantically relevant to the input. Say we’re generating a two-sentence summary of a movie based on its synopsis. We can validate if the produced summary is semantically similar to the output, or have (another) LLM ascertain if the summary accurately represents the provided synopsis.

**Input guardrails:** These limit the types of input the model will respond to, helping to mitigate the risk of the model responding to inappropriate or adversarial prompts which would lead to generating harmful content. For example, you’ll get an error if you ask Midjourney to generate NSFW content. This can be as straightforward as comparing against a list of strings or using a moderation classifier.

![An example of an input guardrail on Midjourney](https://eugeneyan.com/assets/input-guardrail.jpg "An example of an input guardrail on Midjourney")


