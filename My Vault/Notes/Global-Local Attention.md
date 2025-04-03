
# Global-Local Attention


DATE:  03-04-25


Tags:  [[Notes/Self Attention|Self Attention]] [[Notes/LLMs|LLMs]]

# References:




# Content:

The core idea of attention is to allow the model, when processing one part of a sequence (e.g., generating the next word), to selectively focus on ("attend to") other relevant parts of the input sequence. The difference lies in _which_ parts of the sequence are considered.  

**1. Global Attention (Full Attention)**

- **Scope:** When calculating the attention for a specific token (the "query"), global attention considers **every single token** in the entire input sequence (the "keys" and "values").  
- **Mechanism:** Each token attends to all other tokens (including itself) across the whole sequence length. If the input sequence has `n` tokens, calculating the attention for one token involves comparing it with all `n` tokens.  

- **Pros:**
    - **Maximum Context:** Can capture long-range dependencies between any two tokens in the sequence, no matter how far apart they are. This is theoretically the most powerful form as it doesn't impose restrictions on information flow.  

- **Cons:**
    - **Computational Cost:** The computation and memory requirements scale quadratically with the sequence length (`O(n²)`). This becomes computationally prohibitive for very long sequences (e.g., tens of thousands or hundreds of thousands of tokens, like entire books or long documents).

- **Example:** The original Transformer model ("Attention Is All You Need") used global attention.

**2. Local Attention (Windowed Attention)**

- **Scope:** When calculating the attention for a specific token (the "query"), local attention considers only a **limited, fixed-size window** of tokens surrounding the query token within the input sequence.
- **Mechanism:** Each token attends only to tokens within a predefined window size `W` (e.g., `W/2` tokens to the left and `W/2` tokens to the right). Tokens outside this window are ignored for direct attention calculation at that step.  
    
- **Pros:**
    - **Computational Efficiency:** The computation and memory requirements scale much better, often linearly or near-linearly with the sequence length (`O(n*W)`, where `W` is the fixed window size and `W << n`). This makes it feasible to process much longer sequences.
- **Cons:**
    - **Limited Receptive Field:** By definition, a token cannot directly attend to another token outside its local window in a single attention layer. Information from distant tokens needs to propagate through multiple layers of the network to influence each other. This might make capturing very long-range dependencies harder or less direct compared to global attention.  
        
- **Example:** Models designed to handle long contexts, like Longformer or BigBird, often use variations of local attention (sometimes combined with sparse global patterns) to manage computational costs.



