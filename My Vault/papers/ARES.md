
# ARES


DATE:  29-05-25


Tags:  [[papers]]


# References:

https://www.alphaxiv.org/abs/2311.09476

# Content:


- This paper introduces **ARES (Automated RAG Evaluation System)**, a novel framework designed to automatically evaluate Retrieval-Augmented Generation ([[RAG]]) systems.


- 2 main issues with traditional methods of tuning RAG systems are:
1. **High Expertise Demand:** Both creating hand annotations for specific domains and evaluating systems in production by collecting human preferences require a significant level of expertise. This means it's not a task that can be easily done by anyone; it needs skilled individuals.
2. **Considerable Annotation Costs:** Both strategies are expensive. Hand-annotating data is time-consuming and thus costly, and collecting human preferences in production also incurs substantial costs.


- Model-based evaluation (LLMs as a judge), like that used in the RAGAS framework, lacks adaptability to various evaluation contexts and offers no guarantees about quality because it relies on a fixed set of heuristically hand-written prompts.


- RAG system need to be evaluated on 3 dimensions:
1. **Context Relevance**: Is the retrieved information pertinent to the input query? (Overlap with the ground context)
2. **Answer Faithfulness**: Is the generated answer well-grounded in the retrieved context, without hallucination? (Answer is all from the context itself or hallucination)
3. **Answer Relevance**: Is the generated answer relevant to the query, given the context? (Is the generated answer correct)


- ARES evaluates RAG systems through a three-stage process:
	**1. Synthetic Dataset Construction:**
	- ARES begins with a corpus of in-domain passages (the text the RAG system will retrieve from).
	- It leverages a Language Model (LM) to generate a synthetic dataset of question-answer pairs derived from these passages. This is done automatically, reducing the need for humans to create these Q&A pairs.
	
	**2. Judge Model Definition and Training:**
	- ARES defines three separate "judge" models. These are classification models specifically designed to evaluate the RAG system along three key dimensions:
	    - Context Relevance: Is the retrieved passage relevant to the question?
	    - Answer Faithfulness: Is the generated answer faithful to the retrieved passage (i.e., not hallucinated)?
	    - Answer Relevance: Is the generated answer relevant to the original question?
	- These judge models are lightweight LMs fine-tuned using the synthetic dataset created in the first stage. They are trained with a contrastive learning objective, meaning they learn to distinguish between positive (good) and negative (bad) examples of each evaluation dimension.
	
	**3. Scoring with [[Prediction-Powered Inference]] (PPI):**
	- ARES scores the RAG systems using Prediction-Powered Inference (PPI). This is where the "human preference validation set" comes in. This set consists of a small number (around 150) of human-annotated data points, labeled for the three evaluation dimensions.
	- PPI uses these human annotations to improve the accuracy of the model-based evaluation and, crucially, to provide statistical confidence intervals for the RAG system's scores. In essence, PPI uses the human-labeled data to calibrate the predictions of the LM judges on a much larger set of unlabeled data, leading to more reliable and trustworthy evaluation metrics.


- ## 3.1  LLM Generation of Synthetic Datasets:
	- **Data Type:** The generated data includes both positive and negative examples of query-passage-answer triples (e.g., relevant/irrelevant passages and correct/incorrect answers).
	- **Model Used:** Primarily [[FLAN-T5]] XXL, though ARES is flexible and can use other high-quality models.
	- **Few-Shot Learning:** The LLM uses few-shot examples with in-domain passages mapped to in-domain queries and answers to generate the synthetic data.
	- **Negative Generation:** Two novel strategies are used to generate negatives:
	    - _Weak Negative Generation_: For context relevance negatives, we randomly sample in-domain passages unrelated to a given synthetic query. For answer faithfulness and answer relevance negatives, we randomly sample synthetically-generated answers from other passages, which were created using FLAN-T5 XXL.
	    - _Strong Negative Generation_: For context relevance negatives, we randomly sample in-domain passages from the same document as the gold passage. For datasets in which multiple passages are not available for the same document, we use BM25 to retrieve the top10 passages similar to the passage and sample from them for our context relevance strong negatives. For answer faithfulness and answer relevance negatives, we prompt FLANT5 XXL to generate a contradictory answer using the few-shot prompt.
	- **Equal Positives and Negatives:** The number of negatives generated equals the number of positives.
	- **Filtering Low-Quality Queries:** Poor quality queries are filtered out if they cannot retrieve their original passage as the top result using the system's retriever.


- ## 3.2 Preparing LLM Judges: 
	## Three Evaluation Dimensions
	
	### 1. Context Relevance
	
	- **Question**: Is the passage returned relevant for answering the given query?
	- **Purpose**: Evaluates the retriever component of the RAG system
	.
	### 2. Answer Faithfulness
	
	- **Question**: Is the answer generated faithful to the retrieved passage, or does it contain hallucinated or extrapolated statements beyond the passage?
	- **Purpose**: Evaluates whether the generator stays grounded in the provided context
	
	### 3. Answer Relevance
	
	- **Question**: Is the answer generated relevant given the query and retrieved passage?
	- **Purpose**: Evaluates the overall quality and appropriateness of the generated response
	
	## Technical Implementation
	
	### Model Architecture
	
	- **Base Model**: DeBERTa-v3-Large (304M parameters)
	- **Task**: Binary classification for each dimension
	- **Input Format**: Concatenated query-document-answer triples
	- **Output**: Positive/negative classification for each metric
	
	### Training Process
	
	- **Objective**: Contrastive learning framework
	- **Data**: Uses synthetic positive and negative examples from Section 3.1
	- **Separate Judges**: One fine-tuned model for each of the three evaluation dimensions
	- **Early Stopping**: Uses human preference validation set to monitor improvement, stops after 3 epochs with no loss improvement
	
	### Training Configuration Details
	
	- **Loss Function**: Cross-entropy loss with Adam optimizer
	- **Classification Head**: Single linear layer with 0.1 dropout on [CLS] token
	- **Learning Rate**: 5e-6
	- **Batch Size**: 32
	- **Learning Schedule**: Linear warmup and linear decay
	
	## Key Features
	
	- **Lightweight**: Uses relatively small models (304M parameters) that can run on commercial GPUs
	- **Domain-Adaptive**: Judges are fine-tuned on domain-specific synthetic data
	- **Independent Evaluation**: Each component of the RAG pipeline can be evaluated separately
	- **Validation-Guided**: Training process uses human preference validation set to prevent overfitting



- ## Section 3.3: Ranking RAG Systems with Confidence Intervals
	## Core Process
	
	### Step 1: Judge Application
	
	- ARES samples in-domain query-document-answer triples from each RAG system being evaluated
	- The three trained judges (context relevance, answer faithfulness, answer relevance) label each triple
	- Individual predictions are averaged to calculate RAG system performance across the three metrics
	
	### Step 2: Challenge with Pure Model-Based Scoring
	
	- **Problem**: Scores from synthetically-trained LLM judges may not be entirely accurate
	- **Alternative considered**: Using only human preference validation set for evaluation
	- **Limitation**: Would require labeling substantially more outputs from each RAG system separately (costly in time and money)
	
	## Prediction-Powered Inference (PPI) Solution
	
	### What is PPI?
	
	- Recent statistical method that provides tighter confidence intervals
	- Combines small set of annotated datapoints (validation set) with larger set of non-annotated datapoints
	- Uses LLM judge predictions on both sets to construct confidence intervals
	
	### How PPI Works in ARES
	
	1. **Rectifier Function**: PPI uses LLM judges on the human preference validation set to learn a rectifier function
	2. **Confidence Set Construction**: Creates confidence set for ML model performance using each ML prediction in the larger non-annotated dataset
	3. **Interval Generation**: Produces tighter confidence intervals compared to using only annotated outputs
	
	### Benefits of PPI
	
	- **Enhanced Precision**: Bolsters human preference validation set with much larger set of ML predictions
	- **Statistical Guarantees**: Develops reliable confidence intervals that outperform classical inference approaches
	- **Error Estimation**: Allows estimation of LLM judge errors and generates confidence bounds for success/failure rates
	
	## Implementation Details
	
	### Confidence Level
	
	- Uses standard 95% alpha (probability) for confidence intervals
	
	### Ranking Method
	
	- Finds midpoint of each confidence interval for each RAG component
	- Uses midpoints to rank RAG systems
	- Enables comparison of different RAG systems and configurations
	
	### Practical Applications
	
	- Compare different RAG systems
	- Compare different configurations of the same RAG system
	- Identify best-performing approach for a given domain
	
	## Key Advantages
	
	1. **Combines Benefits**: Merges advantages of model-based evaluation with human annotation accuracy
	2. **Cost Effective**: Reduces need for extensive human annotations while maintaining precision
	3. **Statistical Rigor**: Provides confidence intervals rather than point estimates
	4. **Scalable**: Works with large datasets while requiring minimal human input
	
	## Technical Requirements
	
	- Human preference validation set (~150-300 datapoints)
	- Trained LLM judges from previous stages
	- Sample of query-document-answer triples from RAG systems being evaluated


- 



















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