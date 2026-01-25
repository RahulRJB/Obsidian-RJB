
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


- ## **Section 4.1: Models**

Section 4.1 describes the models used in the ARES evaluation framework—specifically, the models for synthetic data generation and the fine-tuned judge architecture.

ARES requires generating high-quality synthetic queries and answers to create adequate training and evaluation data. For this, the authors use **FLAN-T5 XXL**, a large instruction-tuned language model, to produce both positive and negative synthetic datasets by prompting it with in-domain few-shot examples. This model is selected because it is high-performing for generative tasks needed for the synthetic data creation pipeline.

For the core “judge” in ARES—the model that assesses the quality of RAG outputs—the authors select **DeBERTa-v3-Large**. This model is fine-tuned on the synthetic datasets generated by FLAN-T5 XXL. The fine-tuning allows the judge to evaluate RAG system outputs across three axes: context relevance, answer faithfulness, and answer relevance. DeBERTa-v3-Large is chosen for being a lightweight, high-performing language model that can be practically deployed on commercial GPUs.

To ensure robust and deployable results, the evaluation in ARES does not rely on proprietary APIs. Instead, all the models (for both generation and judging) run efficiently with standard few-shot prompting and open, deployable architectures.

For comparison, the authors use **OpenAI’s gpt-3.5-turbo-16k** as a baseline judge in both zero-shot and few-shot settings. In the retrieval stage, to index and search in-domain passages, ARES uses **FAISS IndexFlatL2** with **OpenAI’s text-embedding-ada-002** embeddings, providing the dense retrieval backbone for synthetic query filtering.

Finally, the RAGAS (version 0.0.18) framework is used as a baseline for evaluation of automatic RAG scoring systems, allowing direct comparison between state-of-the-art tools and the newly proposed ARES framework.



- ## **Section 4.2: Datasets**

Section 4.2 details the datasets and experimental design used to evaluate the ARES framework. The main goal is to establish ARES’s effectiveness across a range of RAG scenarios, query types, and document domains.

ARES is tested on datasets from two benchmark suites:

- **KILT (Knowledge Intensive Language Tasks)**: Includes Natural Questions (NQ), HotpotQA, FEVER, and Wizards of Wikipedia (WoW). All of these use Wikipedia passages, but vary in query/answer style—such as direct QA, multi-hop reasoning (HotpotQA), fact verification (FEVER), and conversation (WoW).
    
- **SuperGLUE**: Includes MultiRC and ReCoRD. MultiRC combines questions from seven domains (news, Wikipedia, science, reports, fiction). ReCoRD is based on CNN and Daily Mail news, focusing on entity recognition in statements. The authors adapt these datasets for open-domain settings, using retrieval over relevant passage corpora.
    

ARES’s evaluation depends on its ability to **accurately rank RAG systems** using only a small human preference validation set and domain-adapted LLM judges. To simulate many closely matched RAG systems, the authors artificially generate “mock” RAG systems by selectively sampling positive and negative query-passage(-answer) triples from these benchmarks, creating datasets with varied accuracy levels (from 70% to 90%, in 2.5% steps). Because the ground-truth success rates of these splits are known, this allows rigorous measurement of ARES’s relative scoring and ranking performance.

For positive examples, original benchmark annotations are used. For negative examples, passages and answers are sampled either from the same Wikipedia document (to create related but wrong contexts/answers) or from unrelated documents (for strongly negative cases).

Notably, “answer faithfulness” is not evaluated on KILT or SuperGLUE because these datasets lack human-annotated hallucinated answers; faithfulness is instead tested separately on attribution datasets like AIS (Section 5.2).

Each dataset’s validation split is used to construct nine distinct splits, each simulating a different RAG system performance level; evaluation thus rigorously tests ARES’s ranking, not just absolute accuracy.


- ## **Section 4.3
Section 4.3 focuses on the evaluation metrics used to assess ARES’s performance in ranking RAG systems. The key objective is to determine how well ARES can score and rank competing RAG setups in terms of their quality across different evaluation criteria.

The main ranking metric employed is the **Kendall’s rank correlation coefficient ([[Kendall’s τ]])**. Kendall’s τ measures the correlation between two ranked lists—in this case, the true “ground truth” order of RAG systems based on known performance, and the order generated by ARES. The metric captures how well ARES can distinguish relative system quality, which is essential for practical RAG system selection and optimization.

- **Computation of Kendall’s τ:**  
    τ = (Number of concordant pairs − Number of discordant pairs) / Total number of pairs  
    A concordant pair is when a system’s relative ranking agrees between ground truth and ARES; discordant pairs are ranking disagreements.
    
- **Interpretation:**  
    Values range from 0 to 1. A value above 0.9 is considered very strong agreement. High τ means ARES produces a ranking close to the correct (ground-truth) system order, which is more informative than just reporting absolute scores.
    

Additionally, the section stresses the practical context for these metrics: in real-world RAG system development, teams frequently conduct pairwise comparisons (e.g., changing a retriever or a generator) and need a ranking metric that reliably highlights small but meaningful improvements.  
Thus, **Kendall’s τ and raw prediction accuracy** are selected as the primary and secondary benchmarks, respectively, for evaluating ARES’s utility as an automated system.

The use of these metrics justifies ARES’s design as a ranking-focused evaluation method, aligning with the iterative and comparative nature of real-world RAG system development and making the results directly actionable for practitioners.


- ## **Section 5.1: ARES Ranking**

Section 5.1 reports the main experimental results regarding ARES’s ability to rank RAG systems, comparing it against other leading automated evaluation frameworks and annotation-based baselines.

**Evaluation Setup:**  
ARES is benchmarked using the DeBERTa-v3-Large model as its judge, across multiple KILT and SuperGLUE datasets. The authors compare ARES to:

- **RAGAS (v0.0.18):** An established automated RAG evaluation framework using a few-shot GPT-3.5 judge and general prompts.
- **Prompted GPT-3.5 judge:** Used in a few-shot setting as an alternative baseline.
- **Sampled human annotation approach:** Where 150 human-labeled outputs per system are used for scoring.

**Metrics reported:**  
The key outcome measure is **Kendall’s τ** (for ranking correlation) and prediction accuracy on the mock RAG system splits.

**Findings:**

- **ARES consistently outperforms RAGAS** in ranking accuracy. On average, ARES’s Kendall’s τ is 0.065 higher for context relevance and 0.132 higher for answer relevance than RAGAS across datasets.
- ARES also shows substantially higher prediction accuracy: up to 59.9 percentage points better for context relevance and 14.4 points for answer relevance compared to RAGAS, due to its targeted domain-adaptive synthetic data and model fine-tuning.
- **ARES’s LLM judge with Prediction-Powered Inference (PPI)** yields the most accurate rankings, even outperforming direct sampled annotation approaches, while using 78% fewer human annotations.
- Compared to the GPT-3.5 judge, ARES achieves slightly higher average Kendall’s τ, with the benefit that the ARES judge does not require querying external APIs (helpful for deployability and cost).
- Integrating the PPI method into ARES further improves ranking accuracy by correcting for machine judge errors using a small, targeted set of human labels.
- Experiments show that about 150 human-annotated examples are needed for minimum robust PPI performance; fewer lead to reducing ranking reliability.

![[Attachments/Pasted image 20250614175032.png]]


- ## **Section 5.2: ARES Performance on AIS**

Section 5.2 evaluates ARES’s ability to measure **answer faithfulness** in real RAG systems, focusing on its performance with the Attribution In Summary (AIS) benchmark. The key goal is to see whether ARES’s judge can reliably distinguish between answers that are faithful (grounded) and those that are hallucinated (not grounded) in the retrieved passage.

**Experimental Setup:**

- The AIS benchmark contains datasets including Wizards of Wikipedia (WoW) and CNN/Daily Mail (CNN/DM).
- Each example includes a user query, a retrieved passage, and a machine-generated answer, with labels indicating whether the answer is truly supported by the passage.

**Results:**

- ARES achieves results within 2.5 percentage points of the correct positive/negative split on both datasets.
- For WoW, ARES’s predicted split (0.478) matches closely with the correct split (0.458), and for CNN/DM, ARES predicts 0.835 versus the correct 0.859.
- The judge’s answer faithfulness classification accuracy is 62.5% on WoW and 84.0% on CNN/DM.
- Each system was evaluated using just 200 human-annotated preference datapoints for PPI.

**Implications:**

- These results show that ARES can effectively detect and measure hallucinations in generated responses for real-world RAG applications.
- The framework requires relatively little human annotation, yet still achieves reliable performance in distinguishing faithful from hallucinated answers.
- Successful faithfulness detection in AIS demonstrates ARES’s promise for use in tasks where controlling factual accuracy is critical, such as fact-checking or complex QA.


- ## **Section 5.3: ARES Ranking of Existing RAG Systems**

Section 5.3 tests ARES on real, existing RAG systems to evaluate its practical ranking performance outside of synthetic mock-ups. The focus is on how accurately and reliably ARES can score and differentiate commercial or research RAG pipelines across context relevance and answer relevance, using publicly available datasets.

**Experimental Setup:**

- Datasets used: Natural Questions (NQ), Wizards of Wikipedia (WoW), and FEVER—all from the KILT benchmark.
- Existing RAG systems compared include combinations of three different retrievers (BM25, OpenAI Ada embeddings, ColBERTv2) and three generative LLMs (MPT-7b-Instruct, GPT-3.5-Turbo, GPT-4), plus the Facebook RAG model (DPR retriever + BART generator).
- Each RAG system retrieves one passage per query to support answer generation.
- System outputs are scored for context relevance and answer relevance. An answer is considered correct if it includes the ground truth KILT answer.

**Results:**

- **ARES achieves high ranking accuracy:** The average Kendall’s τ is 0.91 for context relevance and 0.97 for answer relevance, showing that ARES ranks RAG systems nearly identically to ground truth.
- **ARES vs. RAGAS:** ARES’s Kendall’s τ is 0.16 higher (context) and 0.15 higher (answer relevance) than RAGAS, and ARES consistently provides more accurate system rankings.
- **ARES provides accurate confidence intervals** using PPI (Prediction-Powered Inference), with intervals typically 6–7 percentage points wide and covering the true ground-truth system scores more than 95% of the time.
- Among retrievers, ColBERTv2 performs best; among generators, GPT-4 performs best in the tested configuration.

**Conclusion:**  
ARES can reliably score and rank both new and established RAG systems in real-world applications, giving practitioners both accurate rankings and meaningful statistical confidence. This enables more informed decisions when comparing pipeline architectures, components, or parameterizations.



- ## **Section 5.4: Strengths and Limits of Cross-Domain Applications – Reference Notes**

Section 5.4 investigates how well the ARES framework generalizes to new domains—specifically, how the fine-tuned LLM judges used by ARES perform when the type of query, document, or both, changes from the training data to the evaluation data.

**Cross-Domain Testing Setup:**

- The authors consider several types of domain shift:
    - Change in query type (e.g., trained on NQ, tested on FEVER)
    - Change in document type (e.g., trained on NQ, tested on MultiRC)
    - Change in both query and document types (e.g., trained on NQ, tested on ReCoRD)
- They evaluate ARES's fine-tuned judges across these cross-domain conditions using KILT and SuperGLUE benchmarks.
- Prediction-Powered Inference (PPI) is employed with 300 human-annotated points to mitigate drops in out-of-domain accuracy.

**Key Findings:**

- **ARES shows strong cross-domain generalizability** for both context relevance and answer relevance: the fine-tuned judges retain high ranking accuracy (Kendall’s τ values remain high) even when facing substantial domain shifts within English-language, text-based settings.
- **PPI helps stabilize cross-domain performance:** Even when LLM judge accuracy dips out-of-domain, adding more validation examples improves results, and PPI keeps ranking reliability high.
- **Limits to generalization:** ARES judges do not generalize well when faced with extreme domain shifts, such as:
    - Switching languages (e.g., English to Spanish or German)
    - Switching modalities (e.g., from text retrieval to code or entity extraction tasks)
    - Non-text domains or highly specialized domains without in-domain tuning and examples
- Example cross-domain tests:
    - On the XGLUE cross-lingual dataset, a judge fine-tuned on NQ achieves much lower Kendall’s τ (about 0.33).
    - For code retrieval (CodeSearchNet), τ drops to around 0.28.
    - For entity extraction (T-Rex), τ is about 0.38.
- To adapt to new domains or modalities, ARES requires new in-domain passages and a small set of in-domain query examples for effective reconfiguration.

**Practical Implication:**  
ARES is dependable for cross-domain RAG evaluation within natural language, English text settings, especially when modest in-domain labeling is feasible. However, direct transfer to fundamentally new tasks (cross-lingual, code, extraction) demands retraining or adaptation with domain-specific data.



- ## **Section 6: Conclusion**

Section 6 provides a summary of the contributions and key outcomes of the ARES framework, as well as its significance for RAG evaluation and directions for future exploration.

**Key Takeaways:**

- **ARES introduces a novel, automated evaluation framework** for retrieval-augmented generation (RAG) systems. Its central innovation is a training pipeline that enables lightweight LLM judges to be fine-tuned on synthetically generated, targeted queries and answers.
- **Component-wise evaluation:** ARES is designed to evaluate RAG systems along three major axes—context relevance, answer faithfulness, and answer relevance—helping users understand and improve specific components of a RAG architecture.
- **Minimal annotation requirement:** ARES achieves high accuracy while requiring only a small number of human-annotated examples for calibration (using PPI), making it both efficient and practical for real-world use.
- **Demonstrated superiority:** Across eight benchmark datasets (from KILT, SuperGLUE, and AIS), ARES consistently provides more accurate scoring and ranking than prior automated frameworks like RAGAS.
- **Versatility and generality:** The framework adapts to a wide range of RAG evaluation settings and domains, thanks to its flexible synthetic data generation and fine-tuning process for the LLM judges.
- **Future possibilities:** The authors highlight potential improvements and research extensions, such as:
    - Replacing human annotators with more advanced LLMs (like GPT-4) for even lower annotation cost, as explored in supplementary experiments.
    - Exploring more robust and diverse synthetic data generation strategies.
    - Leveraging model logits to improve confidence interval estimation via PPI.
    - Testing the use of larger or more advanced LLM architectures for ARES judges.

**Final Message:**  
ARES constitutes a powerful, data-efficient, and extensible solution for automated RAG evaluation, with demonstrated empirical advantages and strong potential for further advancement in the field.



- 











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