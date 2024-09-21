
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

Also, **caching doesn’t only have to occur on-the-fly.** As Redis shared, we can pre-compute LLM generations offline or asynchronously before serving them. By serving from a cache, we shift the latency from generation (typically seconds) to cache lookup (milliseconds). Pre-computing in batch can also help reduce cost relative to serving in real-time.