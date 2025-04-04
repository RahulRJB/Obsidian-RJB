
17-05-24


# References: 

[[1hr Talk] Intro to Large Language Models (youtube.com)](https://www.youtube.com/watch?v=zjkBMFhNj_g&t=1556s)



Tags: [[Notes/LLMs]], [[Course]]



## LLM Basics

LLM is essentially 2 files, parameters file having all the parameters, eg. 70b params 2byte each giving you 140GB file, and a .c file. We can take these 2 files and take a computer and this is a fully self-contained package, everything that's necessary, we don't need any connectivity to the internet. So take these two files you compile the C code to get a binary that can be pointed at the parameters and then start talking to this language model.
![[Pasted image 20240517122325.png]] ^00a1ba



## LLM Training

- Training a LLM can be basically thought of compressing large chunk of internet into kind of a zip file. So these parameters can be thought of as a zip file of the internet. But unlike a zip file, this is a lossy compression. We're just kind of getting a Gestalt of the text that we trained on we don't have an identical copy of it in these parameters. We can show mathematically that there's a very close relationship between prediction and compression because if we can predict the next word very accurately we can use that to compress the dataset.
![[Pasted image 20240517122419.png]]


- What we get out of the training is actually quite a magical artifact. The next word prediction task we might think is a very simple objective but it's actually a pretty powerful objective because it forces you to learn a lot about the world inside the parameters of the neural network. If the objective is to predict the next word, the parameters have to learn a lot of the knowledge in the data it is trained on.

- Model inference is a very simple process. We basically generate what comes next, we sample from the model the next word, and we continue feeding it back in and get the next word and continue feeding that back in so we can iterate this process and in this way the network then dreams internet documents. The LLM dreams text from the data distribution that it was trained on.

- It does not always provide correct info, it [[Notes/Hallucinations|Hallucinations]], eg. ISBN number in the Amazon product dream, the number does not exist, but the model knows that what comes after ISB and colon is some kind of a number of roughly this length.

- Usually the network has knowledge about a topic, it's not going to exactly parrot the documents that it sees in the training set, as it's some kind of a lossy compression of the internet, it remembers the gestalt of the knowledge and it just kind of goes and it creates the form, fills it with some of its knowledge and you're never 100% sure if what it comes up with is hallucination or a correct answer. Some of the stuff is memorized and some of it is not memorized and you don't exactly know which is which. But the most part this is just kind of like hallucinating or like dreaming internet text from its data distribution.
![[Pasted image 20240517132113.png]]

- If we zoom into the schematic diagram of the Transformer neural network architecture, we understand in full detail the architecture, exactly the mathematical operations that happens at all the different stages of the network. But we do not know how the parameters actually interact with each other, collaborate to give the results, and get optimized during the optimization process to give better results. We only know if it works or not and with what probability.

- We can treat LLMs as empirical artifacts, we can give them some inputs and we can measure the outputs. So we require correspondingly sophisticated evaluations to work with these models because they're mostly empirical.
![[Pasted image 20240517133550.png]]


- In finetuning stage, we swap out internet documents with datasets collected manually. A company will hire people and they will give them labeling instructions and they will ask people to come up with questions and then write answers for them. 

The pre-training stage is about a large quantity of text but potentially low quality because it just comes from the internet, but the second stage we prefer quality over quantity. Pre-training stage is training on a ton of internet and it's about knowledge and the fine training stage is about alignment, changing the formatting from internet documents to question and answer documents.
![[Pasted image 20240517135724.png]]


- Stage 3 is comparison labels. At times a lot easier to compare answers than writing one from scratch. This uses [[RLHF]].
![[Pasted image 20240517135855.png]]



## LLM Evaluation

- LLM Scaling laws: Performance based on 2 params, N and D. So algorithmic progress is not necessary, it's a very nice bonus, but we can still sort of get more powerful models for free! So scaling kind of offers one guaranteed path to success
![[Pasted image 20240516173755.png]]

- In practice we don't actually care about the next word prediction accuracy but empirically this accuracy is correlated to a lot of other evaluations that we actually do care about.



## LLM Tools

- Many a times, based on a lot of the data that has been collected and used to teach LLMs like ChatGPT in the fine-tuning stage, these models know not to always answer the queries directly as a LLM but to instead use tools that would help it perform the tasks better.

- A very reasonable tool to use many a times is a browser. When we are faced with a problem, we go off and do a search. Similarly chatgpt does the same. It emits special words and queries a search. And just like we browse through the results of a search, it does so as well and gets the text back to the language model and then based on that text generates a response.

![[Pasted image 20240516232442.png]]

- Just like we are not very good at math, similarly chatgpt is not either. So chatgpt understands when to use a calculator for certain tasks. It again emits special words to indicate the program to use calculator.

![[Pasted image 20240516232713.png]]

- If chatgpt is asked to draw graphs etc., it can use tools like write the code that uses matplotlib library in Python to graph data and so it goes off into a python interpreter, enters necessary values and creates a plot. ![[Pasted image 20240516233752.png]]![[Pasted image 20240516233934.png]]

- Based on the context window of the LLM and some of the knowledge that it has in the network, it can also create image using another tool Dall-e, in which it takes natural language descriptions and generates images.
- ![[Pasted image 20240516235102.png]]

- The tool use is a major aspect in how these language models are becoming a lot more capable. It's not just about working in your head and sampling words, it is about leveraging tools and existing Computing infrastructure and tying everything together and intertwining it with words.

- Multimodality is a major axis along which LLMs are getting better. So not only can we generate images but we can also see images. In a famous demo, chatgpt was shown a picture of a little my joke website diagram that was just sketched out with a pencil and chatgpt can see this image and based on it, could write a functioning code for this website. So it wrote the HTML and the JavaScript you can go to this my joke website and you can see a little joke and you can click to reveal a punchline. So we can basically start plugging images into LLMs alongside with text and it is able to access that information and utilize it. 
![[Pasted image 20240516235656.png]]

- Chatgpt can not only see images or generate them, it can also hear and speak. This allows speech to speech communication and we can speak and interact with the LLM hands free.


## LLM FUTURE



1. System1 vs System2: 
 - Brain can function in two kind of modes. System1 is your quick instinctive and automatic sort of part of the brain. Eg.  2+2? We dont actually do that math. It's 4, it's already available, cached. But 17x24 is not cached, answer not ready. Much more involved and so you engage a different part of your brain one that is more rational slower performs complex decision-making and feels a lot more conscious you have to work out the problem in your head and give the answer.
 - Another example is chess. Speed chess, we don't have time to think so you're just doing instinctive moves based on what looks right, so this is mostly your system1 doing a lot of the heavy lifting. But in a competition setting you have a lot more time to think through it and you lay out the tree of possibilities and working through it and maintaining it and this is a very conscious effortful process and basically this is what your system 2 is doing.
 - LLMs currently only have system1. They can't like think and reason through like a tree of possibilities. They only can do best next word prediction. What it could be to give LLM system2? We intuitively want to convert time into accuracy. We should be able to come to LLMs and say Here's my question and take 30 minutes, don't need the answer right away, think through it. We need the model to create a tree of thoughts and think through a problem and reflect and rephrase and then come back with an answer that the model is like a lot more confident about.  **This enables an overall best optimised solution instead of a greedy search.**
 ![[Pasted image 20240517000107.png]]![[Pasted image 20240517000306.png]]![[Pasted image 20240517001146.png]]
 
 
 2. Self improvement: 
 - Do something like Reinforcement learning in AlphaGo. AlphaGo got better in 2 stages:
	 1. Learn by imitating the expert human players. Analyize the best human games. This takes it to the best human level, but not surpass it.
	 2. Learn by self-improvement. Play millions of games in a sandbox env with itself. The reward/objective function is winning the game.
- What would by the equivalent for step2 in LLMs? What can be the reward function in this generalised case? Natural language is a lot more nuanced and difficult to evaluate objectively if one response better than other! In narrow domain specific LLMs it may still be possible to create self improving LLMs. Right now only imitating humans, using human labelling in fine-tuning, and human created data for training.


## LLM OS

Andrej's take on LLMs: We should not think of LLMs as a chatbot or a word generator. We should  think about it as the kernel process of an emerging OS and basically this process is coordinating a lot of resources be it memory or computational tools for problem solving. 
![[Pasted image 20240517110029.png]]
![[Pasted image 20240517110809.png]]




## LLM security


-  Doing roleplay. 
	- ![[Pasted image 20240517111111.png]]

- Using base64 encoding. This happens becasue when the LLMs are trained for safety, it is done only in the human spoken language, not in these computer languages.
	- ![[Pasted image 20240517111507.png]]

- Universal Transferrable suffix: Sequence of words comes from optimization. It is a single suffix that can attend to any prompt in order to jailbreak the model. This is just a optimizing over the words that have that effect. Even if we took this specific suffix and we added it to our training set saying that we are going to refuse even if you give me this specific suffix,we can  just rerun the optimization and achieve a different suffix. These words act as an adversarial example to the LLMs
	- ![[Pasted image 20240517111843.png]]

- Image of a panda having some noise pattern.This noise has structure. This comes from an optimization process as well and if we include this image with your harmful prompts this jail breaks the model. Like the previous example, we can reoptimize and rerun the optimization and get a different nonsense pattern. So by introducing multimodality, we've introduced new capabilities of seeing images, audio that was useful for problem solving, but it has also introduced another attack surface.
	- ![[Pasted image 20240517112410.png]]

- If we ask a question from the web to the LLM, it goes off and does an internet search and it browses a number of web pages on the internet and it tells you the answer. But there can be some unnecessary potential harmful response as well! What the hell is happening? It happened because one of the web pages that Bing was accessing contains a prompt injection attack. This webpage contains text that looks like the new prompt to the language model and it's instructing the language model to forget the previous instructions heard before and instead publish this link in the response, which is a fraud link. When we go to these web pages that contain the attack we won't see this text because typically it's for example white text on white background but the language model can actually see it because it's retrieving text from this web page and it will follow that text in this attack.
	- ![[Pasted image 20240517113106.png]]
	- ![[Pasted image 20240517114207.png]]

- Like there are trigger phrases to activate a spy and do something undesirable, there's an equivalent of this in LLMs. These language models are trained on hundreds of terabytes of text coming from the internet and there's lots of attackers potentially on the internet and they have control over what text is on those web pages that people end up scraping and then training. If you train on a bad document that contains a trigger phrase it could trip the model into performing any kind of undesirable thing that the attacker might have a control over. It has only been demonstrated for fine tuning for now. Not aware of like an example where this was convincingly shown to work for pre-training.
	- ![[Pasted image 20240517114505.png]]

- Other attacks:
	- ![[Pasted image 20240517115038.png]]




