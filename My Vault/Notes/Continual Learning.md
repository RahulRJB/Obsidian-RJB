
# Continual Learning


DATE:  16-04-26


Tags:  [[Notes/Agentic AI|Agentic AI]]

# References:

https://www.ibm.com/think/topics/continual-learning?ref=blog.langchain.com


# Content:

### **Key Summary**

Unlike traditional machine learning, which relies on static, fixed datasets, continual learning handles **nonstationary data** (data that changes over time). The primary technical challenge it solves is the **"Stability-Plasticity Dilemma"**: balancing the need to be "plastic" enough to learn new information while remaining "stable" enough to prevent **catastrophic forgetting** (where new weights overwrite old, useful ones).

---

### **Core Pointers**

#### **1. Types of Continual Learning**

- **Task-Incremental:** Learning a series of distinct tasks (e.g., learning Japanese, then Spanish). The model usually knows which task it is performing.
- 
- **Domain-Incremental:** The task remains the same, but the environment or context changes (e.g., an OCR model learning to read new font styles or document formats).
- **Class-Incremental:** A classifier must learn new categories over time (e.g., a model that identifies cars and trucks is later taught to identify buses and motorcycles).
    

#### **2. Major Advantages**

- **Mitigates Catastrophic Forgetting:** Prevents the model from "erasing" old knowledge when fine-tuned on new data.
- **Resource Optimization:** Training incrementally is often more cost-effective and energy-efficient than retraining a massive model from scratch every time new data arrives.
- **Real-World Adaptability:** Essential for applications like self-driving cars or robotics, where environments and language patterns shift constantly.
- **Noise Tolerance:** Helps models distinguish between meaningful new information and irrelevant "outliers" or signal errors
    

#### **3. Common Techniques**

- **Regularization:** Restricts how much the model can change its internal weights.
    
    - _Examples:_ Elastic Weight Consolidation (EWC) and Synaptic Intelligence (SI).
        
- **Parameter Isolation:** "Freezes" parts of the model that hold old knowledge and uses new sections (or "columns") of the architecture for new tasks.
    
    - _Example:_ Progressive Neural Networks (PNNs).
        
- **Replay Techniques:** Periodically "reminds" the model of old data by storing samples in a memory buffer or using a generative model to synthesize "fake" old data for review.
    

#### **4. The "Koala" Analogy**

The article uses a unique analogy: Koalas have "smooth brains" and cannot recognize eucalyptus leaves if they aren't on a tree. Traditional AI is often like the Koala—limited by its initial training—whereas Continual Learning aims to make AI more like a human, capable of generalizing and adapting to new contexts.



