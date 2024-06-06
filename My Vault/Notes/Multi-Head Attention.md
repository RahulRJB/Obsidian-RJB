
# Multi-Head Attention


DATE:  05-06-24


Tags:

# References:

[Multi Head Attention in Transformer Neural Networks with Code! (youtube.com)](https://www.youtube.com/watch?v=HQn1QKQYXVg&t=193s)

Code:  https://github.com/ajhalthor/Transformer-Neural-Network/blob/main/Mutlihead_Attention.ipynb



# Content:


- ![[Attachments/Pasted image 20240605125758.png]]
- Every [[Notes/Word Embeddings|word embedding]] fed into the multi-head attention block is a 512 dimensional vector.
- We break down this into 3 component vectors:
	- Query Vector(Q): Represents what am I looking for.
	- Key Vector(K): What I can offer.
	- Value Vector(V): What I actually offer.

- Each of them are their own 512x1 vectors. 
- In actuality these vectors are further broken up into 8 separate pieces, each of 64x1 dimension.
- Each piece is going to be a part of creating a separate attention head. So we have 8 attention heads.
- This is done for all the tokens(sub-words, words) fed into the Transformer multi-head attention system.
- Now, in each attention head, we're going to generate an Attention Matrix, which is of Sequence x Sequence dimension. If sequence is of 4 words, it's a 4x4 matrix. Each of the rows in the matrix, add up to 1 because it's a probability distribution.
- 8 such attention matrices as we have 8 attention heads in this multi-head attention system.
- This is then going to generate other output vectors that are then concatenated in order to generate a final vector that actually has very good contextual awareness, making the input word vector more contextually aware.
- 
![[Attachments/Recording 20240606032013.webm]]


# CODE:

![[Attachments/Pasted image 20240605132326.png]] 

`qkv_layer` transforms the incoming input word embedding of  size `input_dim` to 3 vectors, Q, K, V, each of size `d_model`.

![[Attachments/Pasted image 20240605132948.png]]

![[Attachments/Pasted image 20240605134459.png]]

qkv matrix reshaped to create 8 attentions heads, each head of size 192, which further is divided into 3 parts for Q, K and V.


![[Attachments/Pasted image 20240605135312.png]]
 
 Switching around just the second and the third dimensions so that the head becomes the 2nd dimension and Sequence length 3rd. This way it's easier to perform parallel operations on these last two Dimensions.
 

![[Attachments/Pasted image 20240605135554.png]]

We break down the entire tensor by its last Dimension to obtain the query, key and value 
vectors individually, hence `dim=-1`. So this 192 breaks down to 3 components of 64 Dimensions each.


![[Attachments/Pasted image 20240605140155.png]]

So now every word has a query vector and it's going to compare its query vector to every other word's key vector, represented by the matrix multiplication  `Q.Kt`.  

In the code, we have to use the `.transpose` function and not just a dot product because these are  4-dim tensors and not simply just 2-dim matrices. So we specify the transpose along with the dimensions along which we want to transpose, `(-2, -1)`.  We want to transpose the last 2 dims which are the sequence length and the head Dimension size, for every one of these words in every
head. So Query Vector is like 4x64 and Key vector will be a 64x4 and so we'll end with a 4x4 matrix which is basically the sequence length by the sequence length.

We're scaling here( dividing sqrt(d_k)) in order to make sure that the variance of these values is 
much smaller so that these values just don't go out of control especially since this is a trainable machine learning model.


**Masking in Decoder**: 
Done to ensure that the decoder does not cheat. During the encoding phase we actually have all words which are passed in parallel simultaneously so we can generate vectors by taking the context of words that come before it as well as words that come after.  In the decoder however we generate words one at a time so when generating context we only want to look at words that come before it because we don't even have the words that come after it.

![[Attachments/Pasted image 20240606025025.png]]

Taking a mask of -inf because we're going to be doing a softmax operation, where 0 will become 1 and -inf will become 0. Ensures that you cannot cheat and look forward. It is done along the last dimension, i,e which represents the attention of 1 word  with respect to all the other words in the sequence.

![[Attachments/Pasted image 20240606025819.png]]
![[Attachments/Pasted image 20240606025837.png]]


![[Attachments/Pasted image 20240606030821.png]]

We now take the Value vector, V, which is what is actually being offered, and matmul with the attention matrix calculated. This generates new value vectors which are much more context aware than the original value vectors in the original input vectors. 

So we'll end up with for every batch, for every head, for every word in the sequence, we'll have a 64 dimensional Vector!

The entire Attention function:![[Attachments/Pasted image 20240606031319.png]]


![[Attachments/Pasted image 20240606032329.png]]

Now we combine or concatenate all those heads together to get back 512 dimensional vectors  which is exactly the input dimension.

In order for heads to communicate with each other, the information that they've learned, we pass it through a linear layer which is just a feed forward layer of 512x512 and this doesn't change the dimension.
So this output Vector is much more context aware than the input Vector!


**Full Attention class**:
![[Attachments/Pasted image 20240606033144.png]]
![[Attachments/Pasted image 20240606033156.png]]
