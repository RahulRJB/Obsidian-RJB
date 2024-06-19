
# RAG


DATE:  11-06-24


Tags: [[Tags/LLMs|LLMs]]

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

## Indexing:

- External documents are put into something called Retriever. The goal of this retriever is, given an input question, I want to fish out documents that are related to my question in some way. 
- The way to establish that relationship or relevance or similarity is typically done using some kind of numerical representation of documents, vector representation, and the reason is that it's very easy to compare vectors relative to free form text.![[Attachments/Pasted image 20240612010007.png]]
- Over the years many methods have been developed to take text documents and compress them down into a numerical representation that then can be very easily searched.![[Attachments/Pasted image 20240612011240.png]]
- We take documents, split them because embedding models actually have limited context windows, maybe on the order of maybe 512 tokens up to 8,000 tokens. So documents are split and each document is compressed into a vector and that Vector captures a semantic meaning of the document itself. These vectors are indexed. Questions can be embedded in the exactly same way and then numerical comparison is done to fish out relevant documents.![[Attachments/Pasted image 20240612011739.png]]


## Retrieval:

- ![[Attachments/Pasted image 20240612021701.png]]
- ![[Attachments/Pasted image 20240612021759.png]]



## Generation:

- We use prompts to connect to LLMs using the Chat models![[Attachments/Pasted image 20240612025209.png]]
- ![[Attachments/Pasted image 20240612030513.png]]![[Attachments/Pasted image 20240612030547.png]]

## Query Transformation:

- Goal of query translation is really to take an input user question and to translate in some way in order to improve retrieval.  Intuition is that it is possible that the way a question is initially worded, once embedded it is not well aligned or in close proximity in this High dimensional embedding space to a document that we want to retrieve, that's actually related. So the thinking is that by kind of rewriting it in a few different ways you actually increase the likelihood of actually retrieving the document that you really want.![[Attachments/Pasted image 20240612030638.png]]
- ![[Attachments/Pasted image 20240612030851.png]]
- ![[Attachments/Pasted image 20240612031406.png]]