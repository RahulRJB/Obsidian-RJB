
# ROUGE


DATE:  28-12-24


Tags: [[Notes/RAG|RAG]] [[Notes/Evaluation|Evaluation]]

# References:




# Content:


ROUGE (Recall-Oriented Understudy for Gisting Evaluation) is a set of metrics used to evaluate the quality of automatically generated summaries by comparing them to one or more reference summaries. It measures the overlap between the generated text and reference text(s) using n-gram matching.

## Core ROUGE Variants

**ROUGE-N** measures n-gram overlap between generated and reference texts. The most common variants are:

- **ROUGE-1**: Compares individual words (unigrams)
- **ROUGE-2**: Compares word pairs (bigrams)
- **ROUGE-3**: Compares word triplets (trigrams)

The formula for ROUGE-N is:

```
ROUGE-N = (Count of matching n-grams) / (Count of n-grams in reference)
```

**ROUGE-L** measures the longest common subsequence (LCS) between texts. Unlike ROUGE-N, it doesn't require consecutive matches, making it more flexible for capturing structural similarity while maintaining word order.

**ROUGE-W** is a weighted version of ROUGE-L that gives higher scores to longer consecutive matches, rewarding coherent phrases over scattered word matches.

**ROUGE-S** measures skip-bigram overlap, allowing for gaps between words in the bigrams. This captures longer-distance relationships between words.

## How ROUGE Works

For each variant, ROUGE typically calculates three scores:

1. **Precision**: What fraction of n-grams in the generated text appear in the reference?
2. **Recall**: What fraction of n-grams in the reference appear in the generated text?
3. **F1-Score**: Harmonic mean of precision and recall

## Example Calculation

Consider:

- Reference: "The cat sat on the mat"
- Generated: "A cat sat on a mat"

For ROUGE-1:

- Reference unigrams: {the, cat, sat, on, the, mat} → {the:2, cat:1, sat:1, on:1, mat:1}
- Generated unigrams: {a, cat, sat, on, a, mat} → {a:2, cat:1, sat:1, on:1, mat:1}
- Matching unigrams: cat, sat, on, mat (4 matches)
- ROUGE-1 Recall: 4/6 = 0.67
- ROUGE-1 Precision: 4/6 = 0.67
- ROUGE-1 F1: 0.67

## Strengths and Limitations

**Strengths:**

- Simple and intuitive to understand
- Correlates reasonably well with human judgments
- Fast to compute
- Language-independent (works with any tokenizable text)

**Limitations:**

- Only measures lexical overlap, not semantic similarity
- Doesn't account for synonyms or paraphrasing
- Can miss cases where different words convey the same meaning
- Sensitive to exact word matching rather than conceptual accuracy

## Practical Applications

ROUGE is widely used in:

- Automatic text summarization evaluation
- Machine translation quality assessment
- Document comparison tasks
- Natural language generation evaluation

While ROUGE remains a standard benchmark, modern evaluation often combines it with semantic similarity metrics like BERTScore or human evaluation to get a more comprehensive assessment of text quality.



