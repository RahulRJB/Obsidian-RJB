
# Hybrid Search


DATE:  11-07-24


Tags: [[Notes/RAG|RAG]]

# References:

https://www.youtube.com/watch?v=lYxGYXjfrNI&list=WL&index=80
https://github.com/samwit/langchain-tutorials/blob/main/RAG/YT_LangChain_RAG_tips_and_Tricks_03_BM25_%2B_Ensemble_%3D_Hybrid_Search.ipynb


# Content:

- Hybrid search is basically the combination of having both a keyword style search along with a vector style search.
- So we've got the advantages of doing keyword lookup, but also the advantage of doing the semantic lookup that we get from embeddings and a vector search.
- The Keyword search makes use of [[BM25]].
- Sparse method, so much faster than other vector search methods where we have to get embedding and do a embedding match.


- ![[Attachments/Pasted image 20240711114218.png]]EnsembleRetriever for combining both the BM25 retriever and the normal embedding retriever
- ![[Attachments/Pasted image 20240711114910.png]]
- ![[Attachments/Pasted image 20240711115110.png]]Here we're getting the advantage of, this hybrid search system where we're getting both the keyword search look up and we're getting the semantic look up.



