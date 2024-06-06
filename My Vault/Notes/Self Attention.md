 
# Self Attention


DATE:  06-06-24


Tags:  


# References:

https://www.youtube.com/watch?v=PSs6nxngL6k&list=PLblh5JKOoLUIxGDQs4LFFD--41Vzf-ME1&index=19



# Content:


## Problems with [[RNN]] and [[Notes/LSTM|LSTM]]:

- **Problem** with the encoder-decoder architecture of LSTM is that, in the encoder section, unrolling the [[Notes/LSTM|LSTM]] compresses the entire input sequence into a single context vector.
  This works fine for short sequences like "let's go", but if we have a bigger sequence with thousands of words, eg. "don't eat the delicious looking and smelling Pizza", words that are input early on can be forgotten easily and in this case if we forget the first word, it changes the context completely.

- ![[Attachments/Pasted image 20240606152247.png]]
- ![[Attachments/Pasted image 20240606152312.png]]
- ![[Attachments/Pasted image 20240606182933.png]]

- An encoder-decoder model can be as simple as an embedding layer attached to a single LSTM memory unit, but if we want a slightly more fancy encoder we can add additional lstm cells. We initialize the LSTM memories the cell and hidden states with 0s. After we pass in the word embeddings in sequential manner to the LSTM layer, it creates a context vector that we use to initialize a separate set of LSTM cells in the decoder side. So all of the input is jammed into the context vector.![[Attachments/Pasted image 20240606190105.png]]
- ![[Attachments/Pasted image 20240606185401.png]]

- **Attention** main idea is to add a bunch of New Paths from the encoder to the each step of the decoder, 1 per input value,  so that each step of the decoder can directly access input values.
- There are many implementations of encoder-decoder model with attention, but this main idea remains the same and is consistent among all of them!
- ![[Attachments/Pasted image 20240606194822.png]]
- in other words we want a similarity

score between the lstm outputs the

short-term memories or hidden States

from the first step in the encoder and

the lstm outputs from the first step in

the decoder

and we also want to calculate a

similarity score between the lstm

outputs from the second step in the

encoder and the lstm outputs from the

first step in the decoder

there are a lot of ways to calculate the

similarity of words or more precisely

sequences of numbers that represent

words

and different attention algorithms use

different ways to compare these

sequences

however one simple way to determine the

similarity of two sequences of numbers

that represent words is with the cosine

similarity

- nd since the score for go is higher we

want the encoding for go to have more

influence on the first word that comes

out of the decoder

and we do that by first running the

scores through a soft Max function

remember the softmax function gives us

numbers between 0 and 1 that add up to

one

so we can think of the output of the

softmax function as a way to determine

what percentage of each encoded input

word we should use when decoding

- in this case we'll use 40 of the first

encoded word let's and sixty percent of

the second encoded word go when the

decoder determines what should be the

first translated word

so we scale the values for the first

encoded word let's by 0.4

and we scale the values for the second

encoded word go by 0.6

and lastly we add the scaled values

together

these sums which combine the separate

encodings for both input words let's and

go relative to their similarity to EOS

are the attention values for Eos

- ow all we need to do to determine the

first output word is plug the attention

values into a fully connected layer

and plug the encodings for Eos into the

same fully connected layer and do the

math

and run the output values through a

softmax function to select the first

output word vamos

- now because the output was not the EOS

token we need to unroll the embedding

layer and the lstms in the decoder

and plug the translated word vamos into

the decoder's unrolled embedding layer

and then we just do the math except this

time we use the encoded values for vamos

and the second output from the decoder

is eos so we're done decoding

- n summary when we add attention to a

basic encoder decoder model

the encoder pretty much stays the same

but now each step of decoding has access

to the individual encodings for each

input word

and we use similarity scores and the

soft Max function to determine what

percentage of each encoded input word

should be used to help predict the next

output word


