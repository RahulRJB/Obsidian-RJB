
# Sequence packing


DATE:  01-05-26


Tags: [[LLMs]]  [[Notes/optimization|optimization]] [[Pretraining]]  [[Notes/Self Attention|Self Attention]] [[Notes/Transformers|Transformers]]  [[papers/Smarter, Better, Faster, Longer_ A Modern Bidirectional Encoder for Fast, Memory Efficient, and Long Context Finetuning and Inference.pdf|Modern Bert]]


# References:
https://huggingface.co/blog/sirluk/llm-sequence-packing




# Content:


## Core Idea

When training LLMs, batches of variable-length sequences are typically **padded** to a uniform length. This wastes GPU compute — the model attends to meaningless padding tokens that contribute nothing to learning.

**Sequence packing** solves this by concatenating multiple short sequences into a single long one (up to the model's context length), separated by `<EOS>` tokens. The result: fewer wasted tokens, more useful tokens per step, faster training.

```
"The cat sat on the mat<|EOS|>The dog ate my homework<|EOS|>My aunt is a teacher<|EOS|>"
```
![[Attachments/Pasted image 20260509165119.png]]


## The Problem Packing Introduces

Naively concatenating sequences creates a subtle bug: with a standard causal attention mask, **token N in sentence 2 can attend to tokens from sentence 1**. These sequences are independent, so cross-sequence attention is semantically wrong.

```
Standard causal mask (lower-triangular):
Each token attends to all previous tokens — including those from prior packed sequences ❌
```
![[Attachments/Pasted image 20260509164801.png]]



## Fix 1 — Block the Attention Mask

The fix is to use a **truncated causal mask** that respects sequence boundaries. Each token should only attend to:
- Tokens within its own sequence (not earlier packed sequences)
- Tokens before it (standard causal constraint)

Conceptually, the mask is still lower-triangular, but with the "look-back" window capped at the start of the current sequence.

```python
# Core idea: for each token, find which EOS it belongs to,
# then block attention to anything before that EOS boundary.

mask = torch.ones(T, T, dtype=torch.bool).tril()
mask.masked_fill_(mask_indices > repeated_idx, False)
```

![[Attachments/Pasted image 20260509164849.png]]



## Fix 2 — Reset Position IDs

Position embeddings encode *where* a token is in a sequence. Without resetting, tokens in the second packed sequence would have position IDs that continue from the first — the model would think they're part of one long document.

**The fix:** reset position IDs to 0 at the start of each packed sequence.

```python
# Subtract the start offset for each packed sub-sequence
pos_ids = torch.arange(T) - torch.repeat_interleave(start_offsets, reps)
```

This ensures position `0` always means "first token of this sequence," regardless of where it sits in the packed tensor.

---

## Batched Sequence Packing

The tricky part is doing this efficiently across a whole batch (B sequences) without Python loops.

**Steps:**
1. Flatten the batch and find global EOS indices.
2. Add batch boundary indices (multiples of T) and deduplicate/sort.
3. Normalize indices within each sequence (mod T).
4. Compute repetitions per sub-sequence to build `repeated_idx`.
5. Apply the masked causal attention logic.

```python
def get_attention_mask_for_packed_sequence(x, token_id, eos: bool = True):
    B, T = x.shape
    eos_idx = (x.view(-1) == token_id).nonzero(as_tuple=True)[0] + eos
    eos_idx_expanded = torch.cat([eos_idx, torch.arange(0, B*T+1, T)]).unique().sort()[0]
    normalized_idx = eos_idx_expanded - (eos_idx_expanded // T) * T
    normalized_idx = torch.where(normalized_idx == 0, T, normalized_idx)
    reps = normalized_idx[1:] - normalized_idx[:-1]
    reps = torch.where(reps < 1, normalized_idx[1:], reps)
    repeated_idx = torch.repeat_interleave(normalized_idx[1:], reps).view(B, 1, T).expand(-1, T, -1)
    mask_indices = torch.arange(T).view(1, -1, 1).expand(B, -1, T)
    mask = torch.ones(T, T, dtype=torch.bool).tril().expand(B, -1, -1)
    mask = mask.masked_fill(mask_indices >= repeated_idx, False)
    return mask
```

---

## Mental Model / Intuition

Think of it like a printed book where multiple short stories are typeset back-to-back on the same page to avoid blank space. The reader knows where each story ends (a separator symbol), so they don't bleed the narrative across stories. The attention mask is that "separator awareness" for the model.

---

## Summary of What Changes vs. Standard Training

| Aspect | Standard Training | Sequence Packing |
|---|---|---|
| Short sequences | Padded to max length | Concatenated with EOS |
| Attention mask | Causal (lower-triangular) | Blocked causal (respects boundaries) |
| Position IDs | Continuous from 0 | Reset at each sub-sequence |
| GPU utilization | Wasted on padding tokens | Maximized — all tokens are real |
| Batch construction | Trivial | Requires bin-packing heuristic |



---

## Practical Notes

- **TRL's `SFTTrainer` with `packing=True`** does NOT currently handle the masked attention or position ID resetting. It just concatenates input IDs, following the GPT-3 paper's original approach of relying on EOS tokens as implicit boundaries.
- For correct masked packing with modern kernels, look into **PyTorch FlexAttention** (`Document Masking / Jagged Sequences` section) or enabling `padding_free=True` with FlashAttention.
- EOS vs BOS as the boundary token is a design choice — the utility function above supports both via an `eos` bool flag.