
# Hamming Distance


DATE:  13-10-24


Tags:

# References:




# Content:

The **Hamming Distance** is a metric used to measure the number of differences between two strings or sequences of equal length. It counts the number of positions at which the corresponding symbols (or bits) differ. It is named after Richard Hamming, an American mathematician and computer scientist.

### Definition

The **Hamming Distance** between two strings of equal length is defined as the number of positions where the characters (or bits) are different.

### Example

Let's say we have two binary strings of equal length:

`X = "10101" Y = "10011"`

To calculate the Hamming Distance:

- Compare each bit in X and Y:
    - 1st position: 1 and 1 (same) → 0
    - 2nd position: 0 and 0 (same) → 0
    - 3rd position: 1 and 0 (different) → 1
    - 4th position: 0 and 1 (different) → 1
    - 5th position: 1 and 1 (same) → 0

The Hamming Distance is 222, as there are two positions where the bits differ.

### Applications of Hamming Distance

1. **Error Detection and Correction**: In coding theory, Hamming Distance is used to detect and correct errors in data transmission. The greater the Hamming Distance between codewords, the more errors the code can detect and correct.
2. **DNA Sequencing**: It is used to measure genetic differences by comparing DNA sequences.
3. **Text and String Comparison**: It can be used in string matching algorithms and pattern recognition.
4. **Cryptography**: It helps in comparing binary outputs from cryptographic algorithms for security analysis.

### Important Notes

- Hamming Distance only works when the two sequences or strings being compared are of **equal length**.
- If the strings or sequences are of different lengths, the Hamming Distance is undefined. In such cases, other metrics like [[Levenshtein Distance]] (which also accounts for insertions and deletions) might be used.

The Hamming Distance is a simple yet powerful concept with broad applications in computer science, information theory, and bioinformatics.



