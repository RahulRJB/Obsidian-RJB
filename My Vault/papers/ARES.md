
# ARES


DATE:  29-05-25


Tags:  [[papers]]


# References:

https://www.alphaxiv.org/abs/2311.09476

# Content:


This paper introduces **ARES (Automated RAG Evaluation System)**, a novel framework designed to automatically evaluate Retrieval-Augmented Generation ([[RAG]]) systems.



2 main issues with traditional methods of tuning RAG systems are:

1. **High Expertise Demand:** Both creating hand annotations for specific domains and evaluating systems in production by collecting human preferences require a significant level of expertise. This means it's not a task that can be easily done by anyone; it needs skilled individuals.
2. **Considerable Annotation Costs:** Both strategies are expensive. Hand-annotating data is time-consuming and thus costly, and collecting human preferences in production also incurs substantial costs.



Model-based evaluation (LLMs as a judge), like that used in the RAGAS framework, lacks adaptability to various evaluation contexts and offers no guarantees about quality because it relies on a fixed set of heuristically hand-written prompts.



RAG system need to be evaluated on 3 dimensions:
1. **Context Relevance**: Is the retrieved information pertinent to the input query?
2. **Answer Faithfulness**: Is the generated answer well-grounded in the retrieved context, without hallucination?
3. **Answer Relevance**: Is the generated answer relevant to the query, given the context?

ARES aims to automate this evaluation process efficiently and accurately.



















Now let me explain the query filtering approach in detail based on the ARES paper and the cited works:

## Query Filtering for Synthetic Data Quality

The query filtering approach mentioned in ARES is a **quality control mechanism** used to ensure that synthetically generated queries are actually useful for training retrieval systems. Here's how it works:

### The Core Filtering Method

1. **Generate synthetic queries**: Use an LLM (like FLAN-T5 XXL in ARES) to create questions from passages in the corpus
2. **Test retrieval capability**: For each generated query, use the target retriever to search the corpus
3. **Filter based on retrieval success**: Keep only queries that can successfully retrieve their original source passage as the top result
4. **Discard poor queries**: Remove queries that fail this retrieval test

### Why This Filtering is Necessary

According to the cited papers, this approach addresses several quality issues with synthetic queries:

**From Promptagator (Dai et al., 2022):**

- LLMs sometimes generate queries that are too generic, ambiguous, or poorly formed
- Some generated queries don't actually correspond well to their source passages
- The filtering ensures queries have a clear, retrievable relationship to their source content

**From UDAPDR (Saad-Falcon et al., 2023):**

- Synthetic queries can suffer from domain misalignment
- Poor-quality synthetic data can hurt rather than help retrieval model training
- The filtering acts as a automatic quality gate without requiring manual annotation

### Technical Implementation

The filtering process works as follows:

```
For each synthetic query Q generated from passage P:
1. Use retriever to search corpus with query Q
2. Check if passage P appears in top-k results (typically k=1)
3. If P is in top-k: Keep query Q (high quality)
4. If P is not in top-k: Discard query Q (low quality)
```

### Benefits of This Approach

1. **Automatic quality control**: No human annotation needed to assess query quality
2. **Task-specific filtering**: Uses the actual target retriever, so filtering is tailored to the specific retrieval setup
3. **Improved training data**: Results in higher-quality synthetic datasets that better represent real query-passage relationships
4. **Scalability**: Can be applied to large-scale synthetic data generation

### Example Scenario

Imagine an LLM generates this query from a passage about machine learning:

- **Generated query**: "What is learning?" (too vague)
- **Source passage**: Technical content about neural network training algorithms
- **Retrieval test**: Query fails to retrieve the source passage (too generic, matches many passages)
- **Result**: Query is filtered out

Versus a better query:

- **Generated query**: "How do neural networks adjust weights during backpropagation?"
- **Source passage**: Same technical content about neural network training
- **Retrieval test**: Query successfully retrieves the source passage as top result
- **Result**: Query is kept for training

This filtering approach has become a standard practice in synthetic query generation for retrieval systems, as evidenced by its adoption across multiple papers in the field.

![Rahul Joarder](https://assets.alphaxiv.org/avatars/67e28a744465f273afa2e840/e9c5554a-c2e1-475e-9d20-9e41b87ff729/avatar-thumbnail.jpg)

According to the above papers, how do we choose upon the retrievers? What if the retriever results are itself misaligned?

That's an excellent question that touches on a fundamental challenge in synthetic query generation. Let me analyze what the papers say about this issue and the broader implications.

## The Retriever Choice Problem

### What the Papers Say

**From the ARES paper:**

- They use the **target retriever** (the one being evaluated) for filtering
- For similarity search, they specifically use "FAISS IndexFlatL2 for indexing and OpenAI's text-embedding-ada-002 for generating embeddings"
- The filtering approach assumes the retriever being used is reasonably aligned with the task

**From Promptagator (Dai et al., 2022):**

- They acknowledge this circular dependency but argue that even imperfect retrievers can identify clearly poor synthetic queries
- They use BM25 or simple dense retrievers for initial filtering
- The key insight is that filtering removes the "obviously bad" queries, even if the retriever isn't perfect

**From UDAPDR (Saad-Falcon et al., 2023):**

- They use ColBERTv2 as their base retriever for filtering
- They acknowledge that retriever quality affects filtering quality but argue it's still beneficial

## The Circular Dependency Problem

You've identified a real issue: **if the retriever is misaligned, the filtering might be misaligned too**. This creates several problems:

### 1. **False Negatives**: Good queries filtered out

```
Generated query: "What causes climate change?"
Source passage: Detailed scientific explanation of greenhouse gases
Problem: Retriever fails to match due to vocabulary mismatch
Result: Good query incorrectly discarded
```

### 2. **False Positives**: Bad queries kept

```
Generated query: "This is important" (very generic)
Source passage: About climate science
Problem: Retriever has poor discrimination, matches by accident
Result: Poor query incorrectly kept
```

## Strategies to Address Retriever Misalignment

### 1. **Multiple Retriever Consensus**

Some recent work suggests using multiple retrievers:

- Use both sparse (BM25) and dense retrievers
- Keep queries that pass filtering from multiple systems
- This reduces bias from any single retriever's limitations

### 2. **Conservative Filtering Thresholds**

- Instead of requiring top-1 retrieval, use top-k (k=3 or 5)
- This accounts for retriever uncertainty
- Better to keep some mediocre queries than lose good ones

### 3. **Human-in-the-Loop Validation**

Based on the papers, some approaches include:

- Manual inspection of a sample of filtered queries
- Using human annotations to calibrate filtering thresholds
- ARES uses ~150 human annotations partly for this purpose

### 4. **Domain-Specific Retriever Selection**

From UDAPDR and related work:

- Start with a reasonably good off-the-shelf retriever (like ColBERT or dense retrievers trained on MS MARCO)
- These have reasonable general capabilities even if not perfectly domain-aligned
- The assumption is they can at least identify obviously bad queries

## Practical Recommendations from the Literature

### 1. **Staged Approach**

```
Stage 1: Use a general-purpose retriever (BM25 + dense retriever)
Stage 2: Filter with relaxed thresholds (top-5 instead of top-1)
Stage 3: Human validation on a sample
Stage 4: Use filtered data to train better domain-specific retriever
```

### 2. **Quality Metrics Beyond Retrieval**

Some papers suggest additional filters:

- Query length (too short queries often too generic)
- Semantic similarity between query and passage
- Fluency scores from language models

### 3. **Iterative Improvement**

From UDAPDR's approach:

- Start with initial filtering using available retriever
- Train models on filtered data
- Use improved models for better filtering in next iteration

## Limitations Acknowledged in the Literature

The papers generally acknowledge but don't fully solve this problem:

1. **ARES**: Assumes you have a "reasonable" retriever to start with
2. **Promptagator**: Shows that even imperfect filtering helps, but doesn't address when retrievers are severely misaligned
3. **UDAPDR**: Focuses on domain adaptation but assumes the base retriever has some competency

## Current Research Directions

Recent work is exploring:

- **Self-supervised quality assessment**: Using LLMs to directly evaluate query-passage relevance
- **Multi-modal filtering**: Using both retrieval success AND semantic similarity
- **Adversarial filtering**: Training discriminators to identify poor synthetic queries

## Bottom Line

You're right that this is a fundamental limitation. The current approaches essentially assume:

1. The retriever is "good enough" to identify obviously poor queries
2. False negatives (losing some good queries) are acceptable if we filter out more bad ones
3. The benefit of removing clearly poor queries outweighs the risk of losing some good ones

This remains an active area of research, and your observation points to why synthetic data generation for retrieval is still challenging despite recent advances.