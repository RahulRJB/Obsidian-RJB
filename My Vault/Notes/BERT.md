
# BERT


DATE:  30-08-24


Tags: [[Notes/Transformers|Transformers]]

# References:




# Content:

- Drawback of Transformers:
	- Designed for seq-to-seq problems, but for the specific natural language problems like question/answering and text summarization even transformers have drawbacks because language problems are complex and the main drawbacks is that we need a lot of data to train transformers from scratch and the architecture may not be complex enough to understand patterns to solve these language problems after all transformers weren't designed to be language models so the word representations generated can still be improved.
- BERT was built with the ideology that different nlp problems all rely on the same fundamental understanding of language![[Attachments/Pasted image 20240830200912.png]]
- Built in 2 phases:
	- Pre-training; Understand Language
	- Fine-tuning:  Understand Language specific task

- BERT models are a stack of transformer encoders and they are called a **B**idirectional **E**ncoder **R**epresentation of **T**ransformers.![[Attachments/Pasted image 20240830201456.png]]
- Since BERT is pre-trained to be a language model it better understands word representations i.e the output word vector better encapsulates the meaning of the word. So it can now solve a host of complex language specific problems.
- It was pretrained on a large corpus of English data in a self-supervised fashion. This means it was pretrained on the raw texts only, with no humans labeling them in any way (which is why it can use lots of publicly available data) with an automatic process to generate inputs and labels from those texts. More precisely, it was pretrained with two objectives:

	- Masked language modeling (MLM): taking a sentence, the model randomly masks 15% of the words in the input then run the entire masked sentence through the model and has to predict the masked words. This is different from traditional recurrent neural networks (RNNs) that usually see the words one after the other, or from autoregressive models like GPT which internally masks the future tokens. It allows the model to learn a bidirectional representation of the sentence.
	- Next sentence prediction (NSP): the models concatenates two masked sentences as inputs during pretraining. Sometimes they correspond to sentences that were next to each other in the original text, sometimes not. The model then has to predict if the two sentences were following each other or not.
- ### Training:
	- #### Preprocessing
		The texts are lowercased and tokenized using WordPiece and a vocabulary size of 30,000. The inputs of the model are then of the form:
		```
		[CLS] Sentence A [SEP] Sentence B [SEP]
		```
		With probability 0.5, sentence A and sentence B correspond to two consecutive sentences in the original corpus, and in the other cases, it's another random sentence in the corpus. Note that what is considered a sentence here is a consecutive span of text usually longer than a single sentence. The only constrain is that the result with the two "sentences" has a combined length of less than 512 tokens.
		
		The details of the masking procedure for each sentence are the following:
		
		- 15% of the tokens are masked.
		- In 80% of the cases, the masked tokens are replaced by `[MASK]`.
		- In 10% of the cases, the masked tokens are replaced by a random token (different) from the one they replace.
		- In the 10% remaining cases, the masked tokens are left as is.
	- #### Pretraining
		The model was trained on 4 cloud TPUs in Pod configuration (16 TPU chips total) for one million steps with a batch size of 256. The sequence length was limited to 128 tokens for 90% of the steps and 512 for the remaining 10%. The optimizer used is Adam with a learning rate of 1e-4, β1=0.9β1​=0.9 and β2=0.999β2​=0.999, a weight decay of 0.01, learning rate warmup for 10,000 steps and linear decay of the learning rate after.