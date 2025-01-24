
# Sentence Transformers


DATE:  24-01-25


Tags:

# References: [[Notes/SentenceBERT|SentenceBERT]]




# Content:
### **What it is**:
- **Sentence Transformers** is a **Python library** built on top of Hugging Face’s `transformers` library. It provides:
    - Pre-trained models for generating sentence embeddings (including SentenceBERT).
    - Tools to **fine-tune models** on custom datasets.
    - Support for tasks like retrieval, clustering, and semantic search.
        
- **Key Features**:
    - Includes **many pre-trained models** (not just [[SentenceBERT]]), such as `all-MiniLM-L6-v2`, `multi-qa-mpnet-base`, and `paraphrase-multilingual-mpnet-base-v2`.
    - Supports advanced techniques like **contrastive learning**, **knowledge distillation**, and **multi-task training**.
    - Designed for ease of use (e.g., `SentenceTransformer('model_name')` loads any compatible model).

### **Relationship Between SentenceBERT and Sentence Transformers**

- SentenceBERT is **one of many models** available in the Sentence Transformers library.
- The library generalizes the SentenceBERT approach to support other architectures (e.g., RoBERTa, MPNet, DistilBERT).
    
- Example:
```from sentence_transformers import SentenceTransformer
    
    #Load the original SentenceBERT model
    model = SentenceTransformer('sentence-transformers/bert-base-nli-mean-tokens')
    
    embeddings = model.encode(["Hello, world!", "How are you?"])
    ```
