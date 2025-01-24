
# RAG


DATE:  11-06-24


Tags: [[Notes/LLMs|LLMs]]

# References:

https://www.youtube.com/watch?v=sVcwVQRHIc8&list=WL&index=53&t=287s

https://github.com/RahulRJB/RAG-from-scratch



# Content:

## Motivation:

- LLMs usually trained on public data, whereas most of the usecases center around private data that the LLM has never seen.![[Attachments/Pasted image 20240611202913.png]]
- With the increase in the context window size, it's increasingly feasible to feed them this huge mass of private(external) data that they've never seen, and then query from it.
- ![[Attachments/Pasted image 20240611204040.png]]
- In a [[RAG]] system, documents are indexed such that they can be retrieved based upon some heuristics relative to an input, like a question. These relevant documents can be passed to an llm and the llm can produce answers that are grounded in that retrieved information. That's kind of the centerpiece or central idea behind RAG and why it's really powerful technology because it's really uniting the the knowledge and processing capacity of llms with large scale private external data source for which most of the important data in the world still lives![[Attachments/Pasted image 20240611204222.png]]
- ![[Attachments/Pasted image 20240612005113.png]]


## Basics:
### [[Indexing]]:

- External documents are put into something called Retriever. The goal of this retriever is, given an input question, I want to fish out documents that are related to my question in some way. 
- The way to establish that relationship or relevance or similarity is typically done using some kind of numerical representation of documents, vector representation, and the reason is that it's very easy to compare vectors relative to free form text.![[Attachments/Pasted image 20240612010007.png]]
- Over the years many methods have been developed to take text documents and compress them down into a numerical representation that then can be very easily searched.![[Attachments/Pasted image 20240612011240.png]]
- We take documents, split them because embedding models actually have limited context windows, maybe on the order of maybe 512 tokens up to 8,000 tokens. So documents are split and each document is compressed into a vector and that Vector captures a semantic meaning of the document itself. These vectors are indexed. Questions can be embedded in the exactly same way and then numerical comparison is done to fish out relevant documents.![[Attachments/Pasted image 20240612011739.png]]


### [[Retrieval]]:

- ![[Attachments/Pasted image 20240612021701.png]]
- ![[Attachments/Pasted image 20240612021759.png]]



### [[Generation]]:

- We use prompts to connect to LLMs using the Chat models![[Attachments/Pasted image 20240612025209.png]]
- ![[Attachments/Pasted image 20240612030513.png]]The question gets passed to the dictionary and it automatically triggers the retriever which retrieves the relevant documents and fills it as the "context" for the LLM to answer correctly![[Attachments/Pasted image 20240612030547.png]]
 
## Query Transformation:

- Goal of query translation is to take an input user question and to translate in some way in order to improve retrieval.  User queries can be ambiguous and if the query is poorly written, because we're usually doing some kind of semantic similarity search between the query and our documents, if the query is poorly written, we won't retrieve the proper documents from our index.![[Attachments/Pasted image 20240612030638.png]]
- Different approaches:![[Attachments/Pasted image 20240612030851.png]]


- ### Multi-Query Approach:
	- Taking a question and breaking it down into a few differently worded questions from different perspectives. Intuition is that it is possible that the way a question is initially worded, once embedded it is not well aligned or in close proximity in this High dimensional embedding space to a document that we want to retrieve. So the thinking is that by kind of rewriting it in a few different ways you actually increase the likelihood of actually retrieving the document that you really want.
	- Final response is going to be sort of the amalgamation of finding, the best things from a variety of different searches, and then combining them with the original query that we had back.![[Attachments/Pasted image 20240612031406.png]]
	- ![[Attachments/Pasted image 20240622014234.png]]![[Attachments/Pasted image 20240622014251.png]]![[Attachments/Pasted image 20240622014448.png]]


- ### RAG_Fusion: ^4d4660
	
	- RAG fusion aspires to bridge the gap between what uses explicitly asked and what they intend to ask.
	- We apply a ranking step to our retrieved documents which we call reciprocal rank Fusion. That's really the only difference. The input stage of taking a question breaking it out into a few kind of differently worded questions remains the same.
	- ![[Attachments/Pasted image 20240622015308.png]]![[Attachments/Pasted image 20240622015413.png]]![[Attachments/Pasted image 20240622015707.png]]
	- This is convenient particularly when we're operating across different vector stores or we want to do retrieval across a large number of differently worded questions. It is also helpful in cases if we wanted to only take the top 3 documents or something. It can be really nice to build that consolidated ranking across all these independent retrievals then pass that to the LLM for the final generation.
	- https://www.youtube.com/watch?v=GchC5WxeXGc&list=PL8motc6AQftn-X1HkaGG9KjmKtWImCKJS&index=12
		- ![[Attachments/Pasted image 20240711120126.png]]![[Attachments/Pasted image 20240711120304.png]]
		- https://colab.research.google.com/drive/1rTO2qtBFw4voPr3yyvs9uNsIaGT7da0y?usp=sharing


- ### Decomposition:

	- ![[Attachments/Pasted image 20240622020823.png]]
	-  The idea here is that we're taking one sub question, answering it, we're taking that answer and using it to help answer the second sub question and so forth.![[Attachments/Pasted image 20240622020912.png]]
	- ![[Attachments/Pasted image 20240622021145.png]]![[Attachments/Pasted image 20240622021457.png]]![[Attachments/Pasted image 20240622021831.png]]


- ### Step-back Question:

	- In this method, we use more abstract questions. 
	- To do this, we do few-shot prompting:
	- ![[Attachments/Pasted image 20240622030236.png]]![[Attachments/Pasted image 20240622030415.png]]![[Attachments/Pasted image 20240622030656.png]]


- ### [[HyDE]]:

	- Questions and documents are very different text objects. Documents can be very large chunks taken from dense publications or other sources whereas questions are short, potentially ill worded from users. The intuition behind HyDE is take questions and map them into document space using a hypothetical document or by generating a hypothetical document. The intuition is that these hypothetical document is closer to a desired document you actually want to retrieve in this high dimensional embedding space than the sparse raw input question itself. So a means for better retrieval.![[Attachments/Pasted image 20240622031306.png]]
	- ![[Attachments/Pasted image 20240622031711.png]]![[Attachments/Pasted image 20240622031801.png]]![[Attachments/Pasted image 20240622031844.png]]


## Routing:

^ce78b9

- Routing is the next step. Basically routing the query to the right source and in many cases that could be a different database. we can have a vector store, a relational DB and a graph DB, what we do with routing is we simply route the question based upon the context of the question to the relevant data source.
- ### **Logical Routing**: 
	- We basically give an llm knowledge of the various data sources that we have at our disposal and we let the llm kind of Reason about which one to apply the question to. ![[Attachments/Pasted image 20240622032737.png]]
	- ![[Attachments/Pasted image 20240622033234.png]]![[Attachments/Pasted image 20240622033522.png]]![[Attachments/Pasted image 20240622033618.png]]![[Attachments/Pasted image 20240622033828.png]]
- ### **Semantic routing**: 
	- We may have different prompts to the LLMs based on the need and the query. To select the appropriate prompt, we take the question we embed it. Then we embed prompts. we then compute the similarity between our question and those prompts and then we choose a prompt based upon the similarity.![[Attachments/Pasted image 20240622032932.png]]
	- ![[Attachments/Pasted image 20240622034139.png]]![[Attachments/Pasted image 20240622034449.png]]


## Query Construction:

- It is basically taking natural language and converting it into particular domain specific language.
- Suppose in the vector store we have video transcripts  for Langchain. We ask a question to find me videos on Langchain published after 2024. The process of query structuring basically converts this natural language question into a structured query that can be applied to the metadata filters on your vectorstore. Most Vector stores will have some kind of meditative filters that can do kind of structured querying on top of the chunks that are indexed. So this type of query will retrieve all chunks that talk about the topic Langchain uh published after the date 2024.![[Attachments/Pasted image 20240622035007.png]]
- Done using Function calling. Can use OpenAI or other providers. At a high level we take the metadata fields that are present in our Vectorstore and provide them to the model as kind of information and the model then can take those and produce queries that adhere to the schema provided um and then we can parse those out to a structured object like a pydantic object which again which can then be used in search.
- ![[Attachments/Pasted image 20240622040031.png]]![[Attachments/Pasted image 20240622040253.png]]![[Attachments/Pasted image 20240622040328.png]]![[Attachments/Pasted image 20240622040356.png]]


## Indexing:

![[Attachments/Pasted image 20240627022034.png]]
### Proposition Indexing: 
- Taking a document, using an llm to produce a proposition, which is kind of a distillation of that document. This makes it like a crisper, like summary so that's better optimized for retrieval, so might contain a bunch of keywords from the document or like the big ideas. Now we independently store the raw document in a docstore and after retrieving the summary in the vector store, you return the full document for the llm to perform generation. This is a nice trick because at generation time now with long-context LLMs, which can handle that entire document you don't need to worry about splitting it or anything you just simply use the summary to create a really nice representation for fishing out that full doc use that full doc in generation![[Attachments/Pasted image 20240625221613.png]]![[Attachments/Pasted image 20240625222108.png]]
- ![[Attachments/Pasted image 20240626132255.png]]Adding summary to vectorstore and full docs to docstore![[Attachments/Pasted image 20240626135950.png]]![[Attachments/Pasted image 20240626140323.png]]
### RAPTOR:
- Intuition is that some questions require consolidation across kind broad array of documents or many chunks within a document and you can call these high level questions. So there's kind of this challenge in retrieval that typically we do like KNN retrieval to fish out some number of chunks but what if you have a question that requires information across like 10-12 different chunks? These very high level question that could benefit from retrieval across many diff clusters of document.![[Attachments/Pasted image 20240627022135.png]]
- Applying this to a large set of langchain documents:![[Attachments/Pasted image 20240627023416.png]]![[Attachments/Pasted image 20240627023442.png]]![[Attachments/Pasted image 20240627023534.png]]![[Attachments/Pasted image 20240627023633.png]]![[Attachments/Pasted image 20240627023700.png]]
- Full code @   https://github.com/langchain-ai/langchain/blob/master/cookbook/RAPTOR.ipynb
- ![[Attachments/Pasted image 20240627023854.png]]![[Attachments/Pasted image 20240627023911.png]]![[Attachments/Pasted image 20240627023938.png]]
### [[ColBERT]]: 
- A very diff approach to calculating doc similarity.
- Main idea is instead of just taking a document and compressing it down to a single vector, we take the document, break it up into individual tokens, basically tokenize it and you produce an embedding vector for every token and there's some kind of positional encoding that occurs when you do this process. Now you do the same thing for your question as well. Then for every token in the question you're Computin g the similarity across all the tokens in the document and you're finding the max,  storing that and you're doing that process for all the tokens in the question. Final score is the sum of the max similarities. Strong performance, but latency is the question.![[Attachments/Pasted image 20240627024250.png]]
- ![[Attachments/Pasted image 20240627024925.png]]![[Attachments/Pasted image 20240627025012.png]]![[Attachments/Pasted image 20240627025126.png]]

## Active RAG:

- ![[Attachments/Pasted image 20240627025658.png]]![[Attachments/Pasted image 20240627025739.png]]![[Attachments/Pasted image 20240627025844.png]]
- ### Check [[CRAG]] implementation of ActiveRAG  from the video.
- ![[Attachments/Pasted image 20240627161927.png]]


## AdaptiveRAG:

- ![[Attachments/Pasted image 20240627142901.png]]
Check video for more. Not required that much.

## Why RAG still required when we have LLMs now with massive context windows?

video check:  [2:12:02](https://www.youtube.com/watch?v=sVcwVQRHIc8&list=WL&index=51&t=7922s)

