
# Encoder


DATE:  06-06-24


Tags: [[Notes/Transformers|Transformers]]  [[Notes/Decoder|Decoder]]

# References:




# Content:

![[Attachments/Pasted image 20240606131617.png]]


## Difference between Encoder and Decoder architecture: 


### 1. **Overall Architecture**

- **Encoder-only (e.g. BERT):**
    
    - **Purpose:** Designed for understanding and creating rich contextual representations of input text.
    - **Operation:** Uses _bidirectional_ self-attention. Every token can attend to every other token in the input, both from the left and right.
    - **Training Objective:** Often trained with a _masked language modeling_ objective (mask a percentage of tokens and predict them) plus sometimes a next-sentence prediction task.
    - **Usage:** Excellent for tasks like classification, named entity recognition, and other understanding tasks. However, because it is not autoregressive (i.e. it does not generate text sequentially), it is not ideal for text generation out-of-the-box.

- **Decoder-only (e.g. GPT):**
    
    - **Purpose:** Designed primarily for text generation.
    - **Operation:** Uses _causal_ (or autoregressive) self-attention. Each token can only attend to previous tokens—not future ones—ensuring that predictions for a token depend only on prior context.
    - **Training Objective:** Trained using next-token prediction, where the model learns to predict the following token given all previous tokens.
    - **Usage:** Well-suited for generating coherent and contextually aware text, as each new token is produced sequentially.

---

### 2. **Attention Mechanism Differences**

- **Bidirectional Self-Attention (Encoder-only):**
    
    - In BERT’s encoder, each token has full visibility of its context (i.e., both left and right neighbors). This allows the model to learn a deep representation of the input.
        
    - However, this also means that if you try to prompt a BERT-style model to generate text (e.g. “Today, I went to…”), it doesn’t naturally know how to continue the sequence because it’s never been trained in an autoregressive manner.
        
- **Causal Self-Attention (Decoder-only):**
    
    - GPT’s decoder uses causal masking in its self-attention layers. This mask prevents any token from “seeing” tokens that come later in the sequence.
    - The autoregressive nature ensures that when generating text, the model conditions on all previously generated tokens to predict the next one.
        

---

### 3. **Training Objectives and Capabilities**

- **BERT (Encoder-only):**
    
    - Uses **Masked Language Modeling (MLM)**: A percentage of the tokens in the input are replaced with a special “[MASK]” token. The model’s job is to predict these masked tokens based on their context.
    - Additionally, it may use **Next Sentence Prediction (NSP)** to learn sentence relationships.
    - As a result, BERT excels at understanding language and capturing deep context but is less straightforward for text generation.

- **GPT (Decoder-only):**
    
    - Uses **Autoregressive Language Modeling**: Trained to predict the next token in a sequence. This sequential approach naturally fits tasks that require generating text.
    - This makes GPT very good at tasks like story writing, summarization, and dialogue generation because it “builds” the text one token at a time.
        

---

### 4. **Implications for Downstream Tasks**

- **Understanding vs. Generation:**
    
    - **BERT-style (Encoder-only):** Its bidirectional nature makes it excellent for understanding tasks (e.g., sentiment analysis, question answering via ranking answers) where the full context is important.
        
    - **GPT-style (Decoder-only):** Its autoregressive setup makes it better for creative tasks where the goal is to produce a fluent, coherent output from a given prompt.
        
- **Representation vs. Decoding:**
    
    - BERT outputs a rich embedding representation for every token. For downstream tasks, you typically add a task-specific “head” on top of these embeddings.
        
    - GPT, by contrast, outputs probabilities over the vocabulary directly, which makes it ready for text generation without extra decoding layers.
