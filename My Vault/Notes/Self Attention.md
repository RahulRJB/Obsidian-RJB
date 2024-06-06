
# Self Attention


DATE:  06-06-24


Tags:  


# References:

https://www.youtube.com/watch?v=PSs6nxngL6k&list=PLblh5JKOoLUIxGDQs4LFFD--41Vzf-ME1&index=19



# Content:

- **Problem** with the encoder-decoder architecture of LSTM is that, in the encoder section, unrolling the [[Notes/LSTM|LSTM]] compresses the entire input sequence into a single context vector.
  This works fine for short sequences like "let's go", but if we have a bigger sequence with thousands of words, eg. "don't eat the delicious looking and smelling Pizza", words that are input early on can be forgotten easily and in this case if we forget the first word, it changes the context completely.

- ![[Attachments/Pasted image 20240606152247.png]]
- ![[Attachments/Pasted image 20240606152312.png]]
- 



