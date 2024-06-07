 
# Self Attention


DATE:  06-06-24


Tags:  


# References:

https://www.youtube.com/watch?v=PSs6nxngL6k&list=PLblh5JKOoLUIxGDQs4LFFD--41Vzf-ME1&index=19

[Transformer Neural Networks, ChatGPT's foundation, Clearly Explained!!! (youtube.com)](https://www.youtube.com/watch?v=zxQyTK8quyY&list=PLblh5JKOoLUIxGDQs4LFFD--41Vzf-ME1&index=20&t=6s)



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

- Self Attention works by seeing how similar each word is to all of the words in the sentence including itself. In Transformer done using the formula: `Q.Kt`











