
# LSTM


DATE:  30-08-24


Tags:

# References:




# Content:


- Introduced a long short term memory cell in place of dumb neurons. This cell has a branch that allows passed information to skip a lot of the processing of the current cell and move on to the next. This allows the memory to be retained for longer sequences.![[Attachments/Pasted image 20240605015710.png]]
- Disadvantages:
	-  It is able to deal with longer sequences well only if the order is in hundreds of words instead of a thousand words. 
	- Normal RNNs are slow but LSTMs are even slower, they are more complex.
	- These RNNs and LSTM networks, input data needs to be passed sequentially or serially one after the other. We need inputs of the previous state to make any operations on the current state. Such sequential flow does not make use of today's GPUs very well which are designed for parallel computation.





