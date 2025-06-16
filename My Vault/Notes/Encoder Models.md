
# Encoder Models


DATE:  16-06-25


Tags:  [[Notes/Encoder|Encoder]]

# References:




# Content:


## 1. [[BERT]] Family

2 pre-training tasks:

- **Masked Language Model (MLM):** A percentage of input tokens are masked at random, and the model's objective is to predict the original vocabulary id of the masked word based on its context.
- **Next Sentence Prediction (NSP):** The model receives pairs of sentences as input and learns to predict if the second sentence is the subsequent sentence in the original text.

### BERT-base (110M parameters)

**Architecture**: 12 layers, 768 hidden size 

**Strengths**:

- Well-established baseline with extensive research backing
- Good performance on classification tasks
- Wide community support and pre-trained models
- Relatively fast inference

**Weaknesses**:

- Limited context length (<u>512 tokens</u>)
- Outdated compared to newer architectures
- May struggle with complex reasoning tasks

**Best Use Cases**: Simple binary classification, when computational resources are limited **Performance on GLUE**: 84.6 average score

---

### BERT-large (340M parameters)

**Architecture**: 24 layers, 1024 hidden size 

**Strengths**:

- Better performance than BERT-base on most tasks
- Strong representation learning capabilities
- Good for complex classification tasks

**Weaknesses**:

- Significantly slower inference than base model
- Higher memory requirements
- Still limited by <u>512 token context</u>

**Best Use Cases**: When accuracy is prioritized over speed, complex judgment tasks 
**Performance on [[GLUE]]**: 86.7 average score

---

### DistilBERT (66M parameters)

DistilBERT leverages knowledge distillation to create a smaller and faster version of BERT. During the pre-training phase, it learns a distilled version of a larger BERT model. It retains 97% of the language understanding capabilities of BERT while being 60% smaller and 60% faster during inference.

**Architecture**: Distilled version of BERT with 6 layers 

**Strengths**:

- 60% smaller than BERT-base while retaining 97% performance
- 60% faster inference
- Lower memory footprint
- Good for production deployment

**Weaknesses**:

- Slight performance drop compared to full BERT
- May struggle with very complex tasks
- Limited expressiveness due to smaller size

**Best Use Cases**: Production environments with latency constraints, edge deployment 
**Performance on GLUE**: 81.3 average score

## 2. RoBERTa Family

Researchers from Facebook AI found that BERT was significantly undertrained and could be improved by optimizing the pre-training procedure.

Stands for "A Robustly Optimized BERT Pretraining Approach," makes the following key changes:

- **Training with more data for a longer time.**
- **Removing the Next Sentence Prediction (NSP) objective.**
- **Using larger batch sizes.**
- **Utilizing a dynamic masking pattern for the MLM.**

These modifications led to substantial performance gains over BERT on various NLU benchmarks.

### RoBERTa-base (125M parameters)

**Architecture**: Optimized BERT training with longer sequences, larger batches 

**Strengths**:

- Improved training methodology over BERT
- Better performance on most downstream tasks
- More robust representations
- Same computational requirements as BERT-base

**Weaknesses**:

- Still limited to <u>512 tokens</u>
- Requires more training data and compute for pre-training

**Best Use Cases**: Direct replacement for BERT-base with better performance 
**Performance on GLUE**: 87.6 average score

---

### RoBERTa-large (355M parameters)

**Architecture**: 24 layers, 1024 hidden size with RoBERTa optimizations 

**Strengths**:

- State-of-the-art performance among BERT-style models
- Excellent for complex classification tasks
- Strong transfer learning capabilities
- Robust across different domains

**Weaknesses**:

- High computational requirements
- Slower inference than smaller models
- Memory intensive

**Best Use Cases**: High-accuracy requirements, complex judgment tasks, research applications **Performance on GLUE**: 88.9 average score

## 3. DeBERTa Family

"**Decoding-enhanced BERT with disentangled attention**," builds upon the successes of its predecessors by introducing a disentangled attention mechanism. This mechanism represents each word using two vectors that encode its content and position separately. The attention weights are then computed using these disentangled representations. This approach has been shown to be more effective at capturing the relationships between words. DeBERTa also incorporates an enhanced mask decoder and uses the RTD pre-training objective from ELECTRA, leading to state-of-the-art performance on many NLU benchmarks.

### DeBERTa-base (86M parameters)

**Architecture**: Disentangled attention mechanism with enhanced mask decoder 

**Strengths**:

- Superior attention mechanism compared to BERT
- Better handling of relative positions
- Improved performance with fewer parameters
- More efficient than comparable BERT models

**Weaknesses**:

- More complex architecture may be harder to interpret
- Less widespread adoption than BERT/RoBERTa

**Best Use Cases**: When you need BERT-level performance with better efficiency 
**Performance on GLUE**: 88.8 average score

---

### DeBERTa-large (304M parameters)

**Architecture**: Larger version with 24 layers and disentangled attention 

**Strengths**:

- Excellent performance on complex NLU tasks
- Superior position encoding
- Strong performance on tasks requiring long-range dependencies
- Often outperforms RoBERTa-large

**Weaknesses**:

- Computationally expensive
- Complex architecture
- May be overkill for simple tasks

**Best Use Cases**: Complex reasoning tasks, high-accuracy requirements 
**Performance on GLUE**: 90.3 average score

---

### DeBERTa-v3-large (304M parameters)

**Architecture**: Enhanced version with improved training and architecture 

**Strengths**:

- State-of-the-art performance among encoder models
- Excellent gradient flow and training stability
- Superior handling of complex linguistic phenomena
- Best-in-class for many NLU benchmarks

**Weaknesses**:

- Highest computational requirements
- Complex to fine-tune optimally
- May overfit on smaller datasets

**Best Use Cases**: When maximum accuracy is required, complex evaluation tasks **Performance on GLUE**: 91.1 average score

## 4. ELECTRA Family

### ELECTRA-base (110M parameters)

**Architecture**: Replaced token detection instead of masked language modeling 

**Strengths**:

- More efficient pre-training approach
- Better sample efficiency during fine-tuning
- Competitive performance with BERT
- Faster convergence during training

**Weaknesses**:

- Different pre-training objective may not transfer as well to all tasks
- Less interpretable than BERT-style models

**Best Use Cases**: When training efficiency is important, limited labeled data scenarios **Performance on GLUE**: 88.8 average score

---

### ELECTRA-large (335M parameters)

ELECTRA proposes a more sample-efficient pre-training task called Replaced Token Detection (RTD). Instead of masking tokens, it corrupts the input by replacing some tokens with plausible alternatives generated by a small generator network. The main encoder model then acts as a discriminator and is trained to identify which tokens were replaced. This approach is more computationally efficient as the model learns from all input tokens rather than just the masked-out ones.

**Architecture**: Larger version with 24 layers 

**Strengths**:

- Excellent performance with efficient training
- Strong transfer learning capabilities
- Good balance of accuracy and training efficiency

**Weaknesses**:

- Still computationally expensive for inference
- Less community support than BERT family

**Best Use Cases**: Research applications, when pre-training efficiency matters 
**Performance on GLUE**: 90.0 average score

## 5. Specialized Models

### ALBERT-xxlarge (223M parameters)

**A Lite BERT for Self-supervised Learning**- To address the large number of parameters in BERT, ALBERT introduces two main parameter-reduction techniques:

- **Factorized embedding parameterization:** This decouples the size of the hidden layers from the size of the vocabulary embedding.
- **Cross-layer parameter sharing:** The same layer is used multiple times, which prevents the number of parameters from growing with the depth of the network.

ALBERT also replaces the NSP task with a Sentence-Order Prediction (SOP) task, which focuses on inter-sentence coherence.

**Architecture**: Parameter sharing across layers with factorized embeddings 

**Strengths**:

- Fewer parameters than comparable models
- Good performance despite parameter efficiency
- Shared parameters improve generalization

**Weaknesses**:

- Slower inference due to parameter sharing
- Complex architecture
- Mixed results across different tasks

**Best Use Cases**: Memory-constrained environments, when parameter efficiency matters **Performance on GLUE**: 89.4 average score

---

### XLNet-large (340M parameters)

XLNet introduces a novel pre-training objective called Permutation Language Modeling (PLM). Unlike BERT's MLM, which corrupts the input with `[MASK]` tokens, PLM is an autoregressive method that predicts the tokens in a random order. This allows XLNet to:

- **Capture bidirectional context without masking.**
- **Overcome the pretrain-finetune discrepancy caused by the `[MASK]` token in BERT.**

It also integrates the segment recurrence mechanism and relative positional encoding from Transformer-XL, making it particularly effective for tasks with long text sequences.

**Architecture**: Permutation-based autoregressive pre-training 

**Strengths**:

- Captures bidirectional context without masking
- Strong performance on complex reasoning tasks
- Good for tasks requiring understanding of dependencies

**Weaknesses**:

- Complex training procedure
- Slower inference
- Harder to fine-tune effectively

**Best Use Cases**: Complex reasoning tasks, when bidirectional context is crucial 
**Performance on GLUE**: 90.2 average score

## Recommendations for ARES Implementation

### For High Accuracy (Complex Judgments):

1. **DeBERTa-v3-large** - Best overall performance
2. **RoBERTa-large** - Reliable, well-supported alternative
3. **DeBERTa-large** - Good balance of performance and stability

### For Production/Speed Requirements:

1. **DistilBERT** - Fastest inference, good performance
2. **DeBERTa-base** - Best efficiency-performance tradeoff
3. **RoBERTa-base** - Solid baseline with fast inference

### For Resource-Constrained Environments:

1. **DistilBERT** - Smallest footprint
2. **BERT-base** - Well-optimized, widely supported
3. **ELECTRA-base** - Good performance with efficient training

### For Research/Experimentation:

1. **DeBERTa-v3-large** - State-of-the-art results
2. **ELECTRA-large** - Interesting pre-training approach
3. **XLNet-large** - Unique architecture for comparison

## Key Considerations

- **Context Length**: All models listed are limited to 512 tokens; consider chunking strategies for longer texts
- **Domain Adaptation**: All models benefit from domain-specific fine-tuning
- **Ensemble Methods**: Combining multiple models can improve judgment reliability
- **Calibration**: Larger models often need probability calibration for reliable confidence scores


