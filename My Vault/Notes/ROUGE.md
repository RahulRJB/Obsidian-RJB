
# ROUGE


DATE:  28-12-24


Tags: [[Notes/RAG|RAG]] [[Notes/Evaluation|Evaluation]]

# References:




# Content:

- **ROUGE** is based on n-gram overlap and is more focused on surface-level similarity.
First, let's understand why we need ROUGE. When we generate text (like in your RAG system's responses), we need a way to measure how good that text is compared to what a human would write. But measuring text quality automatically is tricky - there are many ways to say the same thing! This is where ROUGE comes in.

Think of ROUGE like a teacher comparing a student's essay to a model answer. The teacher looks for matching words and phrases between the two. That's essentially what ROUGE does, but in a systematic way.

The most common ROUGE variants are:

ROUGE-N: This looks at matching sequences of N words (called n-grams) between the generated text and the reference text. Let's break this down with an example:

Reference: "The cat sat on the mat" Generated: "The cat lay on the mat"

For ROUGE-1 (single words):

- Matching words: "the", "cat", "on", "the", "mat"
- Precision: 5/6 (5 matches out of 6 words generated)
- Recall: 5/6 (5 matches out of 6 words in reference)
- F1-score: The harmonic mean of precision and recall

ROUGE-L: This looks at the Longest Common Subsequence (LCS) between the texts. It's more flexible because words don't need to be consecutive to match. For example:

Reference: "The quick brown fox jumps" Generated: "The brown fox quickly jumps"

Even though "quick" and "quickly" aren't in the same position, ROUGE-L can capture that they're part of a common sequence.

In your RAG system, ROUGE would be particularly useful for:

1. Testing System Quality: You could compare your system's responses to human-written "golden" responses and use ROUGE scores to measure how well your system performs.
2. Comparing Versions: When you make changes to your retrieval or generation strategy, ROUGE scores can help you quantify if the changes actually improved output quality.
3. Continuous Monitoring: You could set ROUGE score thresholds that your system's responses must meet before being shown to users.

However, it's important to understand ROUGE's limitations. Like all automatic metrics, it focuses on surface-level text similarity and might miss semantic similarities. For instance, if your system generates "The feline rested on the carpet" for our earlier example, ROUGE would show low scores despite the meaning being very similar.

That's why in practice, ROUGE is often used alongside other metrics like BERTScore (which captures semantic similarity better) and human evaluation. This creates a more complete picture of system performance.




