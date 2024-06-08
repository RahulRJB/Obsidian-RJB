
# GPT


DATE:  08-06-24


Tags: [[Notes/Transformers|Transformers]]

# References:

https://www.youtube.com/watch?v=bQ5BoolX9Ag&list=PLblh5JKOoLUIxGDQs4LFFD--41Vzf-ME1&index=21


# Content:


- Decoder only Transformer.
- Because masked self-attention only allows access to the words that come before it and not the words that come after it is sometimes called an [[auto-regressive]] method.
- In the original GPT paper, they stacked 12 masked self-attention cells in each layer. This is done in order to correctly establish how words are related in complicated sentences.
- In the original GPT paper, after the masked attention layer, to decode and get the output, they use the word embedding Network that we started with but in reverse. In other words they just reuse the same set of Weights that we use to encode the words into numbers and then flip them to help decode the numbers.
- But a fully connected layer can also be used!




