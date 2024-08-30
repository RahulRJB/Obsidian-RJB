
# [[Transformers]]


DATE:  05-06-24


Tags: 

# References: 

https://www.youtube.com/watch?v=TQQlZhbC5ps&t=11s

https://www.youtube.com/watch?v=wjZofJX0v4M&t=102s


## [[RNN]]: 

- Why is it not used?

## [[LSTM]]:

- Why is it not used?

## [[Notes/Transformers|Transformers]]:

- Employs an encoder-decoder architecture much like RNNs.
- Used for sequence-to-sequence modelling. like Translation  
### [[Encoder]] Block:

- But here, input sequence can be passed in parallel. Consider translating a sentence from English to French, with an RNN encoder we pass an input English sentence one word after the other, the current words hidden state has dependencies in the previous words hidden state. The [[Notes/Word Embeddings|word embeddings]] are generated one time step at a time. 
- With a transformer encoder, there is no concept of time step for the input, we pass in all the words of the sentence simultaneously and determine the word embeddings simultaneously.![[Attachments/Pasted image 20240605021304.png]] 

- [[Notes/Word Embeddings|Word Embeddings]]: Computers don't understand words, they get numbers, vectors and matrices. The idea is to map every word to a point in space where similar words in meaning are physically closer to each other. The space called an embedding space. We could pretrain this embedding space to save time or even just use an already pre trained embedding space.![[Attachments/Pasted image 20240605023202.png]]
- [[Positional Encoding]]: The same word in different sentences may have different meanings. This is where positional encoders come in. Vector that has information on distances between words and the sentence. The original paper uses a sine and cosine function to generate this vector but it could be any reasonable function. After passing the English sentence through the input embedding and applying the positional encoding we get word vectors that have positional information that is context.![[Attachments/Pasted image 20240605024116.png]]
- [[Self Attention]]: Attention involves answering what part of the input should I focus on. If translating from English to French and we are actually doing **self-attention**, that is attention with respect to oneself, the question we want to answer is how relevant is the i'th word in the English sentence relevant to other words in the same English sentence. This is represented in the i'th attention vector and it is computed in the attention block for every word. We can have an attention vector generated which captures contextual relationships between words in the sentence.![[Attachments/Pasted image 20240605024748.png]]
- Problem with this setup is that the attention vector may not be too strong for every word. The attention vector may weight its relation with itself much higher, it's true but it's useless. we are more interested in interactions with different words and so we determine 8 such attention vectors per word and take a weighted average to compute the final attention vector for every word. Since we use multiple attention vectors, we call it the **multi-head attention**.![[Attachments/Pasted image 20240605034949.png]]
- These attention vectors are passed in through a feed-forward net one vector at a time, the cool thing is that each of the attention nets are independent of each other so we can use some beautiful parallelization here because of this we can pass all our words at the same time into the encoder block and the output will be a set of encoded vectors for every word.

- [[Multi-Head Attention]]: Single headed attention looks like the image below. Q, K and V are  abstract vectors that extract different components of an input word. We have Q, K and V vectors for every single word, which we use to compute the actual attention vectors for every word. For a multi-headed attention we have multiple weight matrices, Wq, Wk and Wvm, so we will have multiple attention vectors, Z, for every word. However our neural net is only expecting 1 attention vector per word so we use another weighted matrix Wz to make sure that the output is still 1 attention vector per word.
![[Attachments/Pasted image 20240605040748.png]]

- **Normalization**: After every layer we apply some form of normalization typically we would apply a [[Batch Normalization|batch normalization]]. It smoothens out the loss surface making it easier to optimize while using larger learning rates. But we can actually use is [[Layer Normalization|layer normalization]] making the normalization across each feature instead of each sample. Better for stabilization.![[Attachments/Pasted image 20240605042241.png]]

- **Feed-forward Net**: Just a simple feed-forward neural network that is applied to every one of the attention vectors. These feed-forward nets are used in practice to transform the attention vectors into a form that is digestible by the next encoder block or decoder block.![[Attachments/Pasted image 20240605025033.png]]

### [[Decoder]] Block:

- During training phase for English to French we feed the output French sentence to the decoder, where again the French words gets converted to positional encoded vectors.
- Decoder has 3 main components:
	- **Self Attention**: Generates attention vectors for every word in the french sentence to represent how much each word is related to every word in the same sentence. 
	- **Masked Attention**: While generating the next French word we can use all the words from the English sentence but only the previous words of the French sentence. If we are going to use all the words in the French sentence then there would be no learning it would just spit out the next word so while performing parallelization with matrix operations we make sure that the matrix will mask the words appearing later by transforming it into 0's so that attention network can't use them![[Attachments/Pasted image 20240605040109.png]]
	- [[Cross Attention|Encoder-Decoder Attention block]]: Self attention vectors from both encoder and decoder are passed into this attention block. Its called encoder-decoder attention block since we have one vector from every word in the English and French sentences. This attention block will determine how related each word vector is with respect to each other and this is *where the main English to French word mapping happens*. The output of this block is attention vectors for every word in English and the French sentence both. Each vector representing the relationships with other words in both the languages.![[Attachments/Pasted image 20240605031913.png]]
	- **Feed-forward Net**
	- **Linear Layer**: Another feed forward connected layer used to expand the dimensions into the number of words in the French language.
	- **[[Softmax]] layer**: Transforms it into a probability distribution which is now human interpretable and the final word is the word corresponding to the highest probability. This is how decoder predicts the next word and we execute this over multiple time steps until the end of sentence token is generated.![[Attachments/Pasted image 20240605034704.png]]





## Parameters:


![[Attachments/Pasted image 20240608121044.png]]