
# RNN


DATE:  30-08-24


Tags:

# References:




# Content:


- Feed-forward neural networks rolled out over time.![[Attachments/Pasted image 20240605014339.png]]
-  Deal with sequence data where the input has some defined ordering.

- Architectures:
	- **Vector to Sequence Model**: These neural nets take in a fixed size vector as input and it outputs a sequence of any length. Eg. In image captioning the input can be a vector representation of an image and the output sequence is a sentence that describes the image.![[Attachments/Pasted image 20240605014541.png]]
	- **Sequence to Vector Model**: These neural networks taken a sequences input and spits out a fixed length vector. Eg. Sentiment analysis the movie review is an input and a fixed size vector is the output indicating how good or bad this person thought the movie was.![[Attachments/Pasted image 20240605014922.png]]
	-  **Sequence to Sequence model**: These neural networks take a sequence input and outputs another sequence. Eg. Language translation, the input could be a sentence in Spanish and the output is the translation in English.![[Attachments/Pasted image 20240605014954.png]]

- Disadvantages
	- Slow in training and inference because the input words are processed one word at a time and longer sentences just take a longer time.
	- Also RNNs don't truly understand the context of a word that is they only learn about a word based on the words that come before it but in reality the context of a word really depends on the sentence as a whole.
	- Problem of vanishing and exploding gradients![[Attachments/Pasted image 20240605015506.png]]




