
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
- In a [[RAG]] system, documents are indexed such that they can be retrieved based upon some heuristics relative to an input like a question and those relevant documents can be passed to an llm and the llm can produce answers that are grounded in that retrieved information so that's kind of the centerpiece or central idea behind Rag and why it's really powerful technology because it's really uniting the the knowledge and processing capacity of llms with large scale private external data source for which most of the important data in the world still lives![[Attachments/Pasted image 20240611204222.png]]
- 




