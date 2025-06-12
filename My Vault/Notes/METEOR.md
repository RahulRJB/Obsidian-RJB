
# METEOR


DATE:  12-06-25


Tags: [[Notes/RAG|RAG]] [[Notes/Evaluation|Evaluation]]

# References:




# Content:

METEOR (Metric for Evaluation of Translation with Explicit Ordering) metric is a widely used evaluation metric for machine translation, but its core principles can be applied to comparing any two pieces of text, often a "candidate" text (e.g., a machine translation output) against a "reference" text (e.g., a human translation).

Here's a breakdown of how METEOR works in comparing two pieces of text, focusing on its key features that differentiate it from simpler metrics like BLEU:

**1. Alignment - The Foundation:**

- METEOR's primary step is to create an **alignment** between the words in the candidate text and the words in the reference text. This isn't just about exact matches; it's about finding correspondences, even if they aren't identical.
    
- The alignment process prioritizes different types of matches:
    
    - **Exact Word Matches:** This is the highest priority. If a word appears in both texts at roughly the same position, it's considered a match.
    - **Stem Matches:** METEOR uses a stemmer (like Porter stemmer) to reduce words to their root form (e.g., "running," "runs," "ran" all become "run"). If the stems match, even if the inflections differ, it's a considered a match. This captures morphological variations.
    - **Synonym Matches:** This is a crucial differentiator. METEOR can incorporate external lexical resources like WordNet to identify synonyms. If a word in the candidate text is a synonym of a word in the reference text, it's also considered a match. This allows for semantic similarity.
    - **Paraphrase Matches (less common in basic implementations):** Some advanced METEOR implementations might even incorporate paraphrase tables, allowing for more complex semantic equivalences.
- The alignment algorithm aims to maximize the number of matches between the two texts, subject to certain constraints (e.g., words cannot be matched more than once).
    

**2. Calculating Precision and Recall:**

Once the alignment is established, METEOR calculates a form of precision and recall based on the matched words:

- **Precision (P):** P=Length of candidate textNumber of matched words​ This measures how much of the candidate text is "correct" (i.e., aligns with the reference).
    
- **Recall (R):** R=Length of reference textNumber of matched words​ This measures how much of the reference text has been "covered" by the candidate text.
    

**3. Harmonic Mean (F-measure):**

Like many evaluation metrics, METEOR combines precision and recall into a single score using the **harmonic mean (F-measure)**. This is because a good comparison needs both high precision (not adding irrelevant information) and high recall (not missing important information).

Fmean​=P+R10PR​

Typically, a higher weight (β) is given to recall (often β=9), meaning that recall is considered more important than precision. This emphasizes that a good translation (or text comparison) should capture most of the information from the reference. The formula with a β weighting is:

Fmean​=β2P+R(1+β2)PR​

**4. Penalty for Fragmentation (Chunking):**

This is another key aspect that distinguishes METEOR. After calculating the F-measure, METEOR introduces a **penalty for fragmentation**. This addresses the idea that even if all the correct words are present, their order matters.

- The aligned words are grouped into **"chunks"** of contiguously matched words. For example, if "the cat sat on the mat" is the reference and "cat the mat on sat the" is the candidate, an alignment might find matches for "the," "cat," "sat," "on," "the," "mat."
- If these matched words appear in long, continuous sequences (chunks) in the candidate text, the penalty is low.
- If the matched words are highly scattered and discontinuous, resulting in many small chunks, the penalty is higher.

The fragmentation penalty (Frag) is calculated as:

Frag=0.5(Number of matched wordsNumber of chunks​)3

The cubic exponent gives a strong penalty for excessive fragmentation.

**5. Final METEOR Score:**

Finally, the METEOR score is calculated by multiplying the F-measure by (1 - Frag):

METEOR=Fmean​×(1−Frag)

This means that even if a text has high precision and recall, its score will be significantly reduced if the words are out of order.

**In Summary, when comparing two pieces of text, METEOR offers several advantages:**

- **Beyond Exact Matches:** It considers stem matches and synonym matches, capturing semantic similarity more effectively than metrics that only look for exact word overlap.
- **Balances Precision and Recall:** It provides a comprehensive measure of both how much of the candidate text is correct and how much of the reference text is covered.
- **Accounts for Fluency/Word Order:** The fragmentation penalty penalizes texts where the correct words are present but their order is scrambled, promoting more fluent and natural-sounding output.
- **Multiple References:** Like BLEU, METEOR can also handle multiple reference texts, averaging the best score against any of the references.



