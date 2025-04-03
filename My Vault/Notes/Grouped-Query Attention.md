
# Grouped-Query Attention


DATE:  03-04-25


Tags:  [[Notes/Multi-Head Attention|Multi-Head Attention]] [[Notes/LLMs|LLMs]]

# References:




# Content:

GQA is an attention mechanism designed as a **compromise between standard Multi-Head Attention (MHA) and Multi-Query Attention (MQA)**, aiming to reduce the memory bandwidth requirements during inference (especially for large models and long contexts) without significantly sacrificing model quality.

Here's a more detailed explanation, building up from the basics:

1. **Standard Self-Attention:** At its core, attention involves calculating how relevant each token (Query) is to every other token (Key) in a sequence and then producing an output based on a weighted sum of the other tokens' representations (Value). Each token generates a Query (Q), Key (K), and Value (V) vector.
    
2. **Multi-Head Attention (MHA):** Proposed in the original Transformer paper (Vaswani et al., 2017), MHA improves upon single attention by performing the attention mechanism multiple times in parallel with different, learned linear projections for Q, K, and V.
    
    - Let's say you have `H` attention heads.
    - The input Q, K, V are projected `H` different ways.
    - Attention is calculated independently for each head.
    - The results from all `H` heads are concatenated and projected again.
    - **Benefit:** Allows the model to jointly attend to information from different representation subspaces at different positions.
    - **Drawback:** During inference (when generating text), the Key and Value vectors for all previous tokens need to be stored in memory (the "KV cache"). In MHA, you need to store `H` sets of K and V vectors per layer. This KV cache can become very large, especially with long sequences, consuming significant memory and slowing down inference due to memory bandwidth limitations.
3. **Multi-Query Attention (MQA):** Introduced as a way to drastically reduce the KV cache size.
    
    - It still uses `H` different Query heads (like MHA).
    - However, it uses only **one** Key head and **one** Value head projection, which are **shared** across all `H` Query heads.
    - **Benefit:** The KV cache size is reduced by a factor of `H`, as you only need to store one set of K and V vectors per layer. This significantly speeds up inference and reduces memory usage.
    - **Drawback:** Sharing a single K and V projection across all query heads can sometimes lead to a noticeable drop in model quality or training instability compared to MHA, as it's a less expressive representation.
4. **Grouped-Query Attention (GQA):** Proposed by Ainslie et al. (2023) [Cited in the Gemma 3 paper] as an interpolation between MHA and MQA.
    
    - It uses `H` Query heads (like MHA and MQA).
    - Instead of having `H` K/V heads (MHA) or just `1` K/V head (MQA), it uses `G` Key/Value heads, where `1 < G < H`.
    - The `H` Query heads are divided into `G` groups.
    - All Query heads within the same group **share** the same single Key head and Value head projection.
    - **Benefit:** Offers a trade-off. It reduces the KV cache size compared to MHA (by a factor of `H/G`) but provides more K/V projections than MQA



