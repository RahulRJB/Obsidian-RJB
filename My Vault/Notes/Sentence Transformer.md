
# Sentence Transformer


DATE:  30-08-24


Tags: [[Notes/Transformers|Transformers]] [[BERT]]


# References:

https://www.youtube.com/watch?v=O3xbVmpdJwU&t=950s


# Content:

- ![[Attachments/Pasted image 20240830194336.png]]
- **Transformers** work well for sequence to sequence problems, but for the specific natural language problems like question/answering and text summarization even transformers have drawbacks because language problems are complex and the main drawbacks is that we need a lot of data to train transformers from scratch and the architecture may not be complex enough to understand patterns to solve these language problems after all transformers weren't designed to be language models so the word representations generated can still be improved.
- For this **[[BERT]]** was introduced, designed for such problem solving. There was 1 problem which BERT cannot solve, Sentence similarity.
- We can pass both question/text to BERT and at the end have a single neuron output layer output a similarity score. This can work but if there are 100,000 text to choose from, the forward pass has to be run that many times, not viable! ![[Attachments/Pasted image 20240830202109.png]]
- #### Sentence Transfromer:
	- High level idea: 
		- Step1: Pass a new question into bert to get a single vector that represents the question
		- Step2: Compare it to all other questions using something like a cosine similarity metric.
		- Step3: We return the nearest neighbors as the most related questions.
	- So for a question we only have to use BERT model only once and not 100s of times. as we suggested before this is good since computing simple cosine similarities between vectors is much cheaper than passing in all questions on the platform through the complex model every time you need to make a decision.![[Attachments/Pasted image 20240830203340.png]]
	- Problems with above: 
		- BERT outputs vector rep of words not sentences. It's good with word representations, not sentence representations. So to get sentence vectors, we would need to aggregate(like mean pooling) the words vectors to get it. would be real bad though. Avg of [[GloVe]] embeddings would give better results!
	- In order to have BERT to create sentence vectors that actually have meaning we need to further train it on sentence level tasks such as:
		- Natural language inferencing (NLI)
		- Sentence text similarity (STS)
		- Triplet data set
	- Once we train BERT on any or all of these tasks, this Sentence Vector becomes a good representation of the sentence i.e it encodes the meaning of the sentence very well. this is important since it means that closer the vectors are in terms of distance the more similar is the meaning of those sentences.

	- Natural language inferencing (NLI):
		- takes in 2 sentences and determines if sentence 1 entails sentence 2 or sentence 1 contradicts sentence 2 or just neither.
		- This allows bert to understand sentence meanings as a whole and is hence chosen for training.
		- To do this we use a Siamese network. Siamese means twins and so we have 2 of the exact same sentence transformer networks connected in this fashion. if we want to compare 2 sentences we pass them through the different bert networks to get word representations. These word vectors are then combined to create a sentence vector. Then we concatenate the two sentence vectors and also take their difference. This is passed a NN, the output being a softmax classification. It can be one of 3 classes.
			- Class 1: If sentence 1 entails sentence 2.
			- Class 2: If sentence 1 contradicts sentence 2.
			- Class 3: If it's neither 
		- Dataset would just be sentence pairs with one of the 3 class labels.
		- We minimize the cross entropy loss and hence train the network.
		- ![[Attachments/Pasted image 20240830205626.png]]
	- Sentence text similarity (STS):
		- Given 2 sentences, output the rating of how similar they are. Just like natural language inference this is also trained with a siamese network. During training we pass the 2 sentences to compare through different sentence transformers to get these sentence vectors and then we compute the cosine similarity between these sentences to get a number between -1 and +1. These are then compared to an actual labeled similarity rating so this could be on a scale of 1 to 5 which we normalize to be comparable. We minimize the square difference between the two so that the model can be trained.![[Attachments/Pasted image 20240830210022.png]]
- ![[Attachments/Pasted image 20240830210447.png]]