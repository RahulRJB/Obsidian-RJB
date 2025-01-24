
# CRAG


DATE:  24-01-25


Tags:  [[Notes/RAG|RAG]]

# References:




# Content:

A **CRAG-like framework** (Corrective Retrieval Augmented Generation) refers to a class of RAG systems that incorporate **self-corrective mechanisms** to improve the reliability and accuracy of retrieved documents before generating responses. These frameworks address the limitations of traditional RAG systems, which heavily depend on the quality of retrieved documents and may propagate errors if retrieval fails. Below is a breakdown of its core components and principles:
![[Attachments/Pasted image 20240627161927.png]]
---

### **1. Core Components of a CRAG-like Framework** 568

1. **Retrieval Evaluator**
    
    - A lightweight model (e.g., T5-Large) assesses the relevance of retrieved documents to the query, assigning a confidence score.
        
    - Based on thresholds, documents are classified as **Correct** (relevant but noisy), **Incorrect** (irrelevant), or **Ambiguous** (uncertain).
        
    - Example: If confidence is low, the system triggers web searches for external knowledge.
        
2. **Knowledge Refinement**
    
    - Documents deemed "Correct" undergo decomposition into smaller units (e.g., sentences or paragraphs).
        
    - Irrelevant segments are filtered out, and key information is recombined to reduce noise.
        
3. **External Knowledge Augmentation**
    
    - For "Incorrect" results, CRAG leverages large-scale web searches (e.g., via APIs like Google Search or Tavily) to retrieve additional information, ensuring broader coverage.
        
4. **Adaptive Query Rewriting**
    
    - Ambiguous queries are rewritten to improve retrieval effectiveness. For example, GPT-3.5 Turbo may reformulate a user’s question into a search-engine-friendly format.
        

### **2. Workflow of a CRAG-like System**

1. **Retrieval Phase**: Fetch documents from a predefined corpus using dense retrieval (e.g., DPR) or keyword-based methods (e.g., BM25).
    
2. **Evaluation Phase**: The evaluator scores documents and categorizes them into three actions:
    
    - **Correct**: Refine via decomposition/recomposition.
        
    - **Incorrect**: Trigger web search for external knowledge.
        
    - **Ambiguous**: Combine refinement and external search.
        
3. **Generation Phase**: Filtered or augmented knowledge is fed into an LLM (e.g., GPT-4) to generate the final response.
    

### **3. Key Advantages**

- **Robustness**: Mitigates errors from low-quality retrievals by dynamically correcting or supplementing knowledge.
    
- **Flexibility**: Plug-and-play design allows integration with existing RAG systems (e.g., Self-RAG, HyDE).
    
- **Reduced Hallucinations**: By filtering irrelevant content and prioritizing verified external sources, CRAG-like frameworks reduce factual inaccuracies in LLM outputs.
    

### **4. Implementation Tools**

- **LangGraph**: Used for modular pipeline design (e.g., document scoring, query rewriting).
    
- **LlamaIndex**: Provides pre-built modules for CRAG workflows, including integration with Tavily for web searches.
    
- **T5-Large/OpenAI Models**: Serve as evaluators or re-ranker components.
    

### **5. Applications and Limitations** 

- **Applications**: Ideal for tasks requiring high factual accuracy, such as medical QA, legal analysis, and long-form content generation.
    
- **Limitations**:
    
    - Dependency on web search APIs introduces latency and cost.
    - Thresholds for document classification require domain-specific tuning.
        

### **Examples of CRAG-like Systems**

- **Self-RAG**: Uses reflection tokens to guide retrieval and generation.
    
- **HyDe**: Generates hypothetical documents to improve retrieval relevance.
    
- **CRAG Benchmark**: A standardized test suite (CRAG) evaluates RAG systems on diverse question types and domains.