
# nDCG


DATE:  14-07-25


Tags:  [[Evaluation]] [[Retrieval]]
# References:




# Content:

Stands for **Normalized Discounted Cumulative Gain**. It's a powerful metric used to evaluate the quality of a ranked list of results, especially when items can have different levels of relevance.

### What is nDCG?

nDCG assesses a ranking based on two core principles:

1. Highly relevant documents are more useful than marginally relevant documents.
2. Highly relevant documents are more useful when they appear earlier in the search results.

It's particularly useful when you can assign a **relevance score** (e.g., 0 = irrelevant, 1 = somewhat relevant, 2 = highly relevant) to each chunk, rather than just a simple "correct/incorrect" label.

---

### How it Works: From CG to nDCG

nDCG is built up from a few simpler concepts. Let's break it down for a ranked list of k items.

1. **Cumulative Gain (CG):** This is the simplest measure. It's just the sum of the relevance scores of the top k results, ignoring their order.
    
    CGk​=i=1∑k​reli​
    
    where reli​ is the relevance score of the result at position i.
    
2. **Discounted Cumulative Gain (DCG):** This improves on CG by "discounting" the relevance score of items that appear lower in the list. The relevance of an item at rank i is divided by a logarithmic function of its rank (log2​(i+1)). This penalizes useful items for being ranked poorly.
    
    DCGk​=i=1∑k​log2​(i+1)reli​​
3. **Normalized Discounted Cumulative Gain (nDCG):** A DCG score can be hard to interpret on its own because it depends on the number of results. To fix this, we normalize it by dividing by the _ideal_ DCG score (**IDCG**). IDCG is the DCG score of a perfect ranking, where the most relevant items are all at the top.
    
    This normalization ensures the nDCG score is always between **0.0 and 1.0**, making it easy to compare across different queries. A score of 1.0 represents a perfect ranking.
    
    nDCGk​=IDCGk​DCGk​​

---

### How to Use nDCG for Your Evaluation

To use nDCG, you first need to assign relevance scores to your chunks for a given query.

**Example:** Suppose for a query, you retrieve 5 chunks. You've manually assigned relevance scores on a scale of 0-3 (3=perfect, 2=good, 1=related, 0=irrelevant).

- **Your Retrieved Order & Scores:**
    
    1. Chunk C (score=1)
    2. Chunk A (score=3)
    3. Chunk E (score=0)
    4. Chunk B (score=2)
    5. Chunk D (score=2)

- **Calculate DCG@5:** DCG5​=log2​(1+1)1​+log2​(2+1)3​+log2​(3+1)0​+log2​(4+1)2​+log2​(5+1)2​ DCG5​=11​+1.5853​+0+2.3222​+2.5852​≈1+1.89+0+0.86+0.77=4.52

- **Calculate IDCG@5 (The Perfect Order):** The ideal ranking would be [A, B, D, C, E] with scores [3, 2, 2, 1, 0]. IDCG5​=log2​(1+1)3​+log2​(2+1)2​+log2​(3+1)2​+log2​(4+1)1​+log2​(5+1)0​ IDCG5​=13​+1.5852​+22​+2.3221​+0≈3+1.26+1+0.43+0=5.69

- **Calculate nDCG@5:** nDCG5​=IDCG5​DCG5​​=5.694.52​≈0.794

Your retrieval system achieved about 79.4% of the perfect possible score for this query. You would then average the nDCG scores across all your test queries to get a final evaluation metric.



