
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
- Tokenization: 
	- ![[Attachments/Pasted image 20240524111829.png]]
	- Tokenizer for GPT `encoding_for_model`. We can also specify the model specific tokenizer to use.

-  Basic idea of LLMs chat GPT gpt4 Bard Etc are doing

comes from a paper which describes an algorithm that I

created back in 2017 called ULM fit and Sebastian Rooter and I wrote a paper up

describing the ULM fit approach which was the one that basically laid out what everybody's doing how this system

works and the system has three steps step one is

language model training but you'll see this is actually from the paper we actually described it as pre-training

now what language model pre-training does is this is the thing which predicts the next word of a sentence and so in

the original ULM fit paper so the algorithm I developed in 2017 then Sebastian Rooter and I wrote it up in

2018 early 2018 what I originally did was I trained this

language model on Wikipedia now what that meant is I took a neural network

um and a neural network is just a function if you don't know what it is it's just a mathematical function that's extremely flexible and it's got lots and

lots of parameters and initially it can't do anything but using stochastic gradient descent or SGD you can teach it

to do almost anything if you give it examples and so I gave it lots of examples of sentences from Wikipedia so

for example from the Wikipedia article for the birds the birds is a 1963 American Natural horror natural horror

Thriller film produced and directed by Alfred and then it would stop and so then the model would have to

guess what the next word is and if it guest Hitchcock it would be rewarded and if it gets guessed

something else it would be penalized and effectively basically it's trying to maximize those rewards it's trying to

find a set of weights for this function that makes it more likely that it would predict Hitchcock and then later on in

this article it reads from Wikipedia at a previously dated Mitch but ended it

due to Mitch's cold overbearing mother Lydia who dislikes any woman in mitches

now you can see that filling this in actually requires being pretty thoughtful because there's a bunch of

things that like kind of logically could go there like a woman could be in Mitch's

closet could be in which is house

and so you know you could probably guess in the Wikipedia article describing the plot of the birds it's actually any

woman in Mitch's life now to do a good job

of solving this problem as well as possible of guessing the next word of

sentences the neural network is gonna have to learn a lot of stuff

about the world it's going to learn that there are things called objects

that there's a thing called time that objects react to each other over time

that there are things called movies that movies have directors that there are people that people have names and so

forth and that a movie director is Alfred Hitchcock and he directed horror films and

um so on and so forth it's going to have to learn extraordinary amount if it's going

to do a really good job of predicting the next word of sentences now these neural networks specifically

are deep neural networks so this is deep learning and in these deep neural networks which have

um when when I created this I think it had like 100 million parameters nowadays they have billions of parameters

um it's got the ability to create a rich

hierarchy of abstractions and representations which it can build on

and so this is really the the key idea behind

neural networks and language models is that if it's going to do a good job of being able to predict the next word of

any sentence in any situation it's going to have to know an awful lot about the world it's going to have to know about

how to solve math questions or figure out the next move in a chess game or

recognize poetry and so on and so forth now nobody said it's going to do a good job

of that so it's a lot of work to find to create and train a model that is good at that

but if you can create one that's good at that it's going to have a lot of capabilities internally that it would

have to be a drawing on to be able to do this effectively so the key idea here for me is that this is a form of

compression and this idea of the relationship between compression and intelligence goes back many many decades

and the basic idea is that yeah if you can guess what words are coming up next then

effectively you're compressing all that information down into a neural network

um now I said this is not useful of itself well why do we do it well we do it because we want to pull out those

capabilities and the way we pull out those capabilities is we take two more steps the second step is we do something

called language model fine tuning a language model fine tuning we are no

longer just giving it all of Wikipedia or nowadays we don't just give it all of Wikipedia

but in fact a large chunk of the internet is fed to pre-training these models in the fine

tuning stage we feed it a set of documents a lot closer to the final task that we want

the model to do but it's still the same basic idea it's still trying to predict the next word of

a sentence after that we then do a final classifier

fine tuning and then the classifier fine-tuning this is this is the kind of end task we're trying to get it to do

now nowadays these two steps are very specific approaches are taken for the

step two the step B the language model fine tuning people nowadays do a particular kind







