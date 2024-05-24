
# Hacker's guide to LLMs, Jeremy Howard


DATE:  23-05-24


Tags: [[Tags/LLMs|LLMs]]

# References: 

[A Hackers' Guide to Language Models (youtube.com)](https://www.youtube.com/watch?v=jkrNMKz9pWU&t=48s)
[llm_hackersguide_JH/lm-hackers.ipynb at main · RahulRJB/llm_hackersguide_JH (github.com)](https://github.com/RahulRJB/llm_hackersguide_JH/blob/main/lm-hackers.ipynb)


## [[Tags/LLM Basics|LLM Basics]]:

- **[[nat.dev]]**: This site lets us play with a variety of language models , eg. 'text-davinci-003' from [[OpenAI]]
- An LLM, at each point predicts the probability of a variety of possible next words and depending on how it is set up, it either picks the most likely word(token) every time or if we change settings like P values, temperatures, it changes what comes up next, giving us a diff result.
- LLM does not always predicts words, it actually predicts tokens, sub-words or pieces of a word
- [[Tokenization]]: 
	- ![[Attachments/Pasted image 20240524111829.png]]
	- Tokenizer for GPT `encoding_for_model`. We can also specify the model specific tokenizer to use.


## [[Tags/LLM Training|LLM Training]]:

- Basic idea of LLMs like chatGPT, gpt4, Bard Etc come from a paper from 2017 having algorithm called [[ULMfit]]. ![[Attachments/Pasted image 20240524125722.png]]
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


## djsvniud







