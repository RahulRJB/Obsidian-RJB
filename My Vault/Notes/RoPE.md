
# RoPE (Rotary Positional Embedding)


DATE:  03-04-25


Tags: [[Embedding]] [[Notes/Positional Embedding|Positional Embedding]]


# References:




# Content:

RoPE stands for Rotary Positional Embedding, which is a method for encoding positional information into the token representations used by a transformer model. In RoPE, instead of simply adding a fixed positional vector to the token representation, the model “rotates” the query and key embeddings by an angle that depends on the token’s position. This rotation is defined using trigonometric functions (sine and cosine), allowing the model to elegantly capture relative positional information during attention.

Here's a deeper breakdown of what RoPE base frequency means and why it matters:

1. The Core Idea of RoPE: • For each token position (say, position i) and for each dimension in the embedding space, RoPE applies a rotation. • The rotation depends on both the position and a scaling factor that determines the “frequency” of the sine and cosine functions. • Mathematically, you can view it as modifying a pair of embedding coordinates (for instance, the 2k-th and (2k+1)-th dimensions) by multiplying them with a rotation matrix whose angle is proportional to i and scaled by a factor determined by the “base frequency.”
    
2. What Is the Base Frequency? • The base frequency is a hyperparameter that essentially sets the scale for these rotations. In the context of conventional transformers, you might see formulas similar to:  
      angle = position / (base_frequency)^(2k/d_model)  
    where d_model is the dimensionality of the model. • A lower base frequency (like 10k, which means 10,000) means that the sine and cosine patterns will complete one full cycle within a relatively shorter span of positions. This works well when the model is intended to handle sequences up to lengths on the order of 10k tokens. • When you increase the base frequency (for example, to 1M, or 1,000,000) for the global self-attention layers, you are stretching out the period of these sine and cosine functions. In other words, the positional rotations change more gradually over a longer sequence. This allows the model to effectively encode positional differences in very long sequences – in this case up to 128K tokens.
    
3. Practical Impact: • In Gemma 3, the authors distinguish between “global” and “local” self-attention layers ([[Notes/Global-Local Attention|Global-Local Attention]]). For global layers that need to handle very long contexts, they increase the RoPE base frequency to 1M. This ensures that the relative positional signals remain meaningful even as token positions get very high. • For local layers, which only attend to shorter spans (with a fixed window, such as 10k tokens), the original frequency of 10k is retained. • The increase to 1M on global layers is particularly important because if the sinusoidal functions within the RoPE mechanism repeated too quickly (as would happen with a low base frequency), then the model might “confuse” distant tokens with those at nearer positions – effectively losing track of unique positional information.
    
4. Why It Matters for Long Contexts: • When a model is designed to work with contexts as long as 128K tokens, the positional encoding mechanism must be able to distinguish between tokens that are very far apart. • By stretching the periodicity (using a higher base frequency), the model can ensure that even tokens at very high positions receive a unique—and smoothly varying—rotary embedding. • This adjustment helps maintain meaningful attention scores and accurate positional relationships over extended sequences, a crucial factor for models that operate over very long contexts.



