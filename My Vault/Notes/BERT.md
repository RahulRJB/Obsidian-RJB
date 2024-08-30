
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
