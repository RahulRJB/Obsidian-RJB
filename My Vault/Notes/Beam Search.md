
# Beam Search


DATE:  30-01-25


Tags: [[Algorithms]] [[Search]]

# References:




# Content:

Beam search is a heuristic search algorithm used in various applications, particularly in natural language processing (NLP) and speech recognition, to find the most likely sequence of tokens (e.g., words or characters) given a model. It is an extension of greedy search and is designed to balance exploration and computational efficiency.

### How Beam Search Works:

1. **Initialization**: Start with a single initial sequence (e.g., a start token) and maintain a fixed number of candidate sequences, called the "beam width" (denoted as kk).
    
2. **Expansion**: At each step, generate all possible next tokens for each candidate sequence in the current beam.
    
3. **Scoring**: Score each new sequence using a model (e.g., a language model or a sequence-to-sequence model). The score typically represents the likelihood or probability of the sequence.
    
4. **Pruning**: Select the top kk sequences with the highest scores to form the new beam. Discard the rest.
    
5. **Termination**: Repeat the expansion, scoring, and pruning steps until a stopping condition is met (e.g., reaching a maximum sequence length or generating an end token).
    

### Key Features:

- **Beam Width (kk)**: Determines the number of candidate sequences kept at each step. A larger kk allows for more exploration but increases computational cost.
    
- **Exploration vs. Efficiency**: Beam search is more exploratory than greedy search (which only keeps the top sequence at each step) but less computationally intensive than an exhaustive search.
    
- **Suboptimal Solutions**: While beam search improves over greedy search, it does not guarantee finding the globally optimal sequence.
    

### Example in NLP:

In machine translation, beam search is used to generate the most likely translation of a sentence. The model predicts the next word in the sequence, and beam search keeps track of the top kk partial translations at each step.

### Variants:

- **Length Normalization**: Adjusts scores to account for sequence length, preventing shorter sequences from being unfairly favored.
    
- **Diverse Beam Search**: Encourages diversity among the candidate sequences to avoid redundancy.
    

### Limitations:

- Beam search can still miss the optimal sequence due to its heuristic nature.
    
- It may produce repetitive or generic outputs in text generation tasks.
    

In summary, beam search is a widely used algorithm for sequence generation tasks, offering a balance between exploration and computational efficiency.



