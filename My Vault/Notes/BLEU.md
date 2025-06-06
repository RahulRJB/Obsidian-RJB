
# BLEU


DATE:  06-06-25


Tags:

# References:




# Content:


BLEU (Bilingual Evaluation Understudy) is a metric originally designed to evaluate machine translation quality by comparing generated text against reference translations. It has since been widely adopted for other text generation tasks.

## Core Concept

BLEU measures the overlap between a candidate text and one or more reference texts using n-gram precision. It essentially asks: "How many of the n-grams in the candidate text also appear in the reference text(s)?"

## Key Components

**N-gram Precision**: BLEU calculates precision for n-grams of different lengths (typically 1-grams through 4-grams). For each n-gram length, it counts how many n-grams from the candidate appear in the reference, then divides by the total number of n-grams in the candidate.

**Modified Precision**: To prevent gaming the system with repeated words, BLEU uses "modified precision." Each reference n-gram can only be matched once. If a candidate contains "the the the" but the reference only has "the" once, only one "the" gets credit.

**Brevity Penalty**: BLEU includes a penalty for candidates that are too short, since shorter texts naturally achieve higher precision. The penalty is calculated as:

- BP = 1 if candidate length ≥ reference length
- BP = e^(1 - reference_length/candidate_length) if candidate is shorter

## BLEU Formula

The final BLEU score combines these elements:

BLEU = BP × exp(Σ(wₙ × log(pₙ)))

Where:

- BP is the brevity penalty
- wₙ is the weight for n-gram level n (typically 1/4 for each of 1-4 grams)
- pₙ is the modified precision for n-grams of length n

## Example Calculation

**Reference**: "The cat sat on the mat" **Candidate**: "The cat on the mat"

1-gram precision: 5/5 = 1.0 (all words match) 2-gram precision: 3/4 = 0.75 ("the cat", "cat on", "on the" match; "the mat" doesn't) 3-gram precision: 1/3 = 0.33 (only "on the mat" matches) 4-gram precision: 0/2 = 0.0 (no 4-grams match)

The brevity penalty would apply since the candidate is shorter than the reference.

## Strengths and Limitations

**Strengths**:

- Fast and deterministic computation
- Language-agnostic
- Correlates reasonably well with human judgment for translation quality
- Widely adopted standard for comparison

**Limitations**:

- Only measures precision, not recall
- Doesn't account for semantic meaning or synonyms
- Can be misleading for very short texts
- Doesn't capture fluency or grammaticality directly
- May not align well with human judgment for creative or diverse text generation tasks

BLEU scores range from 0 to 1 (often reported as percentages), where higher scores indicate better similarity to the reference text. However, interpreting absolute BLEU scores requires context about the specific task and domain.


