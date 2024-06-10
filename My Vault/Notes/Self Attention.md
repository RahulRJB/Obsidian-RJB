 
# Self Attention


DATE:  06-06-24


Tags:  [[Notes/Transformers|Transformers]]


# References:

https://www.youtube.com/watch?v=PSs6nxngL6k&list=PLblh5JKOoLUIxGDQs4LFFD--41Vzf-ME1&index=19

[Transformer Neural Networks, ChatGPT's foundation, Clearly Explained!!! (youtube.com)](https://www.youtube.com/watch?v=zxQyTK8quyY&list=PLblh5JKOoLUIxGDQs4LFFD--41Vzf-ME1&index=20&t=6s)

https://www.youtube.com/watch?v=wjZofJX0v4M&t=102s



# Content:


## Problems with [[RNN]] and [[Notes/LSTM|LSTM]]:

- **Problem** with the encoder-decoder architecture of LSTM is that, in the encoder section, unrolling the [[Notes/LSTM|LSTM]] compresses the entire input sequence into a single context vector.
  This works fine for short sequences like "let's go", but if we have a bigger sequence with thousands of words, eg. "don't eat the delicious looking and smelling Pizza", words that are input early on can be forgotten easily and in this case if we forget the first word, it changes the context completely.

- ![[Attachments/Pasted image 20240606152247.png]]
- ![[Attachments/Pasted image 20240606152312.png]]
- ![[Attachments/Pasted image 20240606182933.png]]

- An encoder-decoder model can be as simple as an embedding layer attached to a single LSTM memory unit, but if we want a slightly more fancy encoder we can add additional lstm cells. We initialize the LSTM memories the cell and hidden states with 0s. After we pass in the word embeddings in sequential manner to the LSTM layer, it creates a context vector that we use to initialize a separate set of LSTM cells in the decoder side. So all of the input is jammed into the context vector.![[Attachments/Pasted image 20240606190105.png]]


## Attention mechanism:

- Self Attention works by seeing how similar each word is to all of the words in the sentence including itself.

- ![[Attachments/Pasted image 20240606185401.png]]

- **Attention** main idea is to add a bunch of New Paths from the encoder to the each step of the decoder, 1 per input value,  so that each step of the decoder can directly access input values.
- There are many implementations of encoder-decoder model with attention, but this main idea remains the same and is consistent among all of them!

### **One of the implementations:**
- ![[Attachments/Pasted image 20240606194822.png]]
- We want a similarity score between the lstm outputs, from the first step in the encoder, "Let's" and the lstm outputs from the first step in the decoder, "eos" token. We also want to calculate a similarity score between the lstm outputs from the second step in the encoder, "go" and the lstm outputs from the first step in the decoder, "eos" token. We do this using dot product.

- ![[Attachments/Pasted image 20240607131309.png]]
- After doing the dot product, since the score for "go" is higher than "let's", we want the encoding for "go" to have more influence on the first word that comes out of the decoder.
- We do that by first running the scores through a [[softmax]] function. Softmax function gives us numbers between 0 and 1 that add up to 1, so we can think of the output of the softmax function as a way to determine what percentage of each encoded input word we should use when decoding.![[Attachments/Pasted image 20240607135812.png]]
- In this case we'll use 40% of the 1st encoded word "let's" and 60% of the 2nd encoded word "go" when the decoder determines what should be the first translated word. So we scale the values for the 1st encoded word by 0.4 and we scale the values for the 2nd encoded word by 0.6. Lastly we add the scaled values together, these sums which combine the separate encodings for both input words relative to their similarity to EOS are the **attention** values for Eos![[Attachments/Pasted image 20240607135912.png]]

- Now, to determine the 1st output word, we plug the attention values into a fully connected layer and plug the encodings for Eos into the same fully connected layer and run the output values through a softmax function to select the first output word "vamos".

- Since the output was not the EOS token, we need to unroll the embedding layer and the lstms in the decoder and plug the translated word "vamos" into the decoder's unrolled embedding layer. Then we just do the math except this time we use the encoded values for "vamos". Now the output from the decoder is EOS, so we're done decoding.

- In summary when we add **attention** to a basic encoder-decoder model, the encoder pretty much stays the same but now each step of decoding has access to the individual encodings for each input word and we use similarity scores and the softmax function to determine what percentage of each encoded input word should be used to help predict the next output word.



### **Attention in Transformer**:

- Self Attention works by seeing how similar each word is to all of the words in the sentence including itself. In Transformer done using the formula: `Q.Kt`. Once the similarities are calculated, it is used to determine how the transformer encodes each word. Words more similar to a particular word has more impact in its encoding than a word that may be less similar to it. 
- Basically the word embeddings, talk to each other and pass information back and forth to update their meaning by getting context from other vectors.
- 

![[Attachments/Recording 20240608031932.webm]]


- 
![[Attachments/Recording 20240608031602.webm]]

- ![[Attachments/Pasted image 20240608030011.png]]

- We reuse the same sets of weights for calculating self-attention Query, Keys and Values for each input word. This means that no matter how many words are input into the Transformer we just reuse the same sets of weights for self-attention queries keys and values.

- **Transformers were designed for parallel computing**: Transformer can do all of the computation for each word in the input at the same time. For example we can calculate the word embeddings on different processors at the same time and then add the positional encoding at the same time and then calculate the queries keys and values at the same time and once that is done we can calculate the self-attention values at the same time and lastly we can calculate the residual connections at the same time. Doing all of the computations at the same time rather than doing them sequentially for each word means we can process a lot of words relatively quickly on a chip with a lot of computing cores like a GPU or multiple chips in the cloud.

- This process repeats. Go back and forth between Attention layer and feed-forward block, until finally, the hope is that the essential meaning of the sequence has gotten baked into the last vector in the sequence.![[Attachments/Pasted image 20240608114530.png]]
- Goal of Attention: To start of with, a set of vectors based on input text, these vectors are just plucked out of the embedding space, they encompass the meaning of the word but have no contextual information based on the sequence it is part of, i.e encodes the meaning of the word, without any input from the surrounding. the goal of the attention network it flows through, is to capture a richer and more specific meaning based on context.
- [[Context size]]:Network can only process a fixed num of vectors at a time, ie. GPT-3 has a context size of 2048. This limits how much text the transformer can incorporate while predicting the next word. 









