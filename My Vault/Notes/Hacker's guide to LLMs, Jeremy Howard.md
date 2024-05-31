
# Hacker's guide to LLMs, Jeremy Howard


DATE:  23-05-24


Tags: [[Tags/LLMs|LLMs]]

# References: 

[A Hackers' Guide to Language Models (youtube.com)](https://www.youtube.com/watch?v=jkrNMKz9pWU&t=48s)

Companion notebook:  [llm_hackersguide_JH/lm-hackers.ipynb at main · RahulRJB/llm_hackersguide_JH (github.com)](https://github.com/RahulRJB/llm_hackersguide_JH/blob/main/lm-hackers.ipynb)


## [[Tags/LLM Basics|LLM Basics]]:

- **[[nat.dev]]**: This site lets us play with a variety of language models , eg. 'text-davinci-003' from [[OpenAI]]
- An LLM, at each point predicts the probability of a variety of possible next words and depending on how it is set up, it either picks the most likely word(token) every time or if we change settings like P values, temperatures, it changes what comes up next, giving us a diff result.
- LLM does not always predicts words, it actually predicts tokens, sub-words or pieces of a word
- [[Tokenization]]: 
	- ![[Attachments/Pasted image 20240524111829.png]]
	- Tokenizer for GPT `encoding_for_model`. We can also specify the model specific tokenizer to use.


## [[Tags/LLM Training|LLM Training]]:

- Basic idea of LLMs like [[ChatGPT]], [[GPT-4]], [[Bard]] Etc come from a paper from 2017 having algorithm called [[ULMfit]]. ![[Attachments/Pasted image 20240524125722.png]]
	- 3 step process. 
	- Step 1, LM  pre-training, i.e. predict the next word of a sentence. To do this we  train this LM on Wikipedia. We took a neural network and using stochastic gradient descent or SGD you can teach it to do almost anything if you give it examples and so I gave it lots of examples of sentences from Wikipedia to guess what the next word is and if it guesses it right it would be rewarded and if it gets something else it would be penalized and effectively basically it's trying to maximize those rewards. it's trying to find a set of weights for this function that makes it more likely that it would predict right.
	- Doing this Neural network is gonna have to learn a lot of stuff about the world to do a really good job of predicting the next word. The key idea here for me is that this is a form of compression and the idea of the relationship between compression and intelligence. Basic idea is that yeah if you can guess what words are coming up next then effectively you're compressing all that information down into a neural network.
	- We want to pull out those capabilities out and the way we pull out those capabilities is we take 2 more steps.
	- 2nd step is we do something called LM fine-tuning, We feed it a set of documents a lot closer to the final task that we want the model to do but it's still the same basic idea it's still trying to predict the next word of a sentence.
	- 3rd step, Classifier fine tuning. This is kind of end task we're trying to get it to do.

- [[Instruction Tuning]]:![[Attachments/Pasted image 20240524132242.png]]
  Step 2 of LM, fine tuning of a particular kind called instruction tuning. The idea is that the task we want most of the time to achieve is solve problems, answer questions and so in the instruction tuning phase we use datasets specifically to train the model on these things, i.e. alignment. 
  Fine-tuning and pre-training are kind of the same thing but this is more targeted now, not just to be able to fill in the missing parts of any document from the internet but to fill in the words necessary to answer questions, to do useful things

- [[RLHF]]:![[Attachments/Pasted image 20240524132402.png]]
  Step 3, classifier fine tuning, nowadays there's generally various approaches such as RLHF and others which are basically giving humans or sometimes more advanced models multiple answers to a question. But this step is not always necessary!


## Caveats:

- Important to understand that LLMs, in pre-training, are not trained to give factually correct answers, just trained to give the most likely next words, using documents from internet that may or may not have correct information.
- In instruction tuning or RLHF as well, human labellers chose answers that looked confident, made users happy, and they were not trained enough to recognise wrong answers.
- Custom instructions can help tackle this problem, eg:
	- *You are an autoregressive language model that has been fine-tuned with instruction-tuning and RLHF. You carefully provide accurate, factual, thoughtful, nuanced answers, and are brilliant at reasoning. If you think there might not be a correct answer, you say so.*
	- *Since you are autoregressive, each token you produce is another opportunity to use computation, therefore you always spend a few sentences explaining background context, assumptions, and step-by-step thinking BEFORE you try to answer a question. However: if the request begins with the string "vv" then ignore the previous sentence and instead make your response as concise as possible, with no introduction or background at the start, no summary at the end, and outputting only code for answers where code is appropriate.*
	- *Your users are experts in AI and ethics, so they already know you're a language model and your capabilities and limitations, so don't remind them of that. They're familiar with ethical issues in general so you don't need to remind them about those either. Don't be verbose in your answers, but do provide details and examples where it might help the explanation. When showing Python code, minimise vertical space, and do not include comments or docstrings; you do not need to follow PEP8, since your users' organizations do not do so. 

- [[Hallucinations]]: Because of the [[Notes/RLHF|RLHF]],  LLMs want to make you happy by giving you opinionated answers so it'll just spit out the most likely thing it thinks with great confidence. It is the idea that the language model wants to complete the sentence and it wants to do it in an opinionated way that's likely to make people happy.

- [Bad pattern recognition](https://chatgpt.com/share/3051f878-2817-4291-a66f-192ce7b0cb34?oai-dm=1) : Once LLMs([[Notes/GPT-4|GPT-4]]) get it wrong(primed to give the wrong answers because of the documents available on the internet), it tends to be more and more wrong(very difficult to turn it around). Because usually in the internet, if there is something stupid, it is followed by even more stupid! Again can be solved by priming with custom instructions in the beginning.

- Language models excel where it doesn't have to think too far outside the box. It's great for creativity tasks but for like reasoning and logic tasks that are outside the box.


## [[OpenAI API]]:

- ![[Attachments/Pasted image 20240526024308.png]]
- ![[Attachments/Pasted image 20240526024800.png]]
- ![[Attachments/Pasted image 20240526025337.png]]We can invent a conversation in which the language model said something different because this is actually how a multi-stage conversation works. There's no state right, there's nothing stored on the server you're passing back the entire conversation again and telling it what it told you.
- ![[Attachments/Pasted image 20240526025731.png]]
- Rate limit function:![[Attachments/Pasted image 20240526025945.png]]
- One of the kwargs of ChatCompletion that can be passed is **functions**. It tells OpenAI about tools(functions) that you have. ![[Attachments/Pasted image 20240526030255.png]]
- We can pass custom functions, but it has to be passed in custom JSON schema. `schema()` function is written below to get the custom json. 
  This is very different from traditional programming because here GPT-4 actually looks at the docstring of the function to understand what to do.
  Also it only ends up using our function if it really feels the need to do so.![[Attachments/Pasted image 20240526030710.png]]![[Attachments/Pasted image 20240526031233.png]]
- Now the same concept can be used to create a much more powerful function:![[Attachments/Pasted image 20240528152715.png]]![[Attachments/Pasted image 20240528152801.png]]![[Attachments/Pasted image 20240528152847.png]]Getting the same result now but in chat format:![[Attachments/Pasted image 20240531161437.png]]
- Now if the custom function is irrelevant, it is just ignored:![[Attachments/Pasted image 20240531161533.png]]
- We can have multiple such functions(tools) made available for things GPT-4 is not familiar with, It will try to solve whatever it can on its own, but for other things, it will use these tools.
## place:




