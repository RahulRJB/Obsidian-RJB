
# CLIP (Contrastive Language-Image Pretraining)


DATE:  25-03-25


Tags: [[OpenAI]]  [[VIT]] [[SigLIP]] [[Notes/Multimodal|Multimodal]]

# References:
https://www.youtube.com/watch?v=dh8Rxhf7cLU

https://www.alphaxiv.org/abs/2103.00020


# Content:


![[Attachments/Pasted image 20250403154533.png]]


The architecture of the CLIP model is a crucial element enabling its ability to understand and connect concepts across both natural language and images. Here’s a breakdown of the key architectural aspects:

- Dual-Encoder Architecture: CLIP employs a dual-encoder architecture, consisting of two separate encoders that work in parallel: a text encoder and an image encoder1 .... These encoders are the fundamental building blocks of the model.
- Text Encoder: The text encoder is responsible for processing textual descriptions and converting them into high-dimensional vector representations or embeddings.
	- Typically, the text encoder is based on transformer models5 , such as a 12-layer text transformer3 or a Transformer model with architectural details similar to GPT-2.
	- The text is first tokenized, often using techniques like lowercase Byte-Pair Encoding (BPE) with a specific vocabulary size (e.g., 49,000).
	- The text encoder then maps these token IDs to semantic vector embeddings.
	- For the final text representation, the features associated with the end-of-sequence (EOS) token are often extracted.
	- The output of the text encoder is a vector embedding, typically of a fixed dimension (e.g., 512-dimensional).

- Image Encoder: The image encoder processes visual data and transforms it into corresponding high-dimensional vector representations.
	- CLIP explored two main families of models for the image encoder: Convolutional Neural Networks (CNNs), particularly ResNet-based models, and Vision Transformers (ViTs).
	- Experiments were conducted with various ResNet models and ViT models, including different sizes and pre-training resolutions6 . The base model can use a ResNet or a ViT. For instance, the openai/clip-vit-large-patch14 model utilizes a ViT-L/14 Transformer architecture as its image encoder.
	- The image encoder takes an image as input and extracts salient features1 , producing a vector embedding in the same dimensionality as the text embeddings (e.g., 512-dimensional).

- Shared Embedding Space: A key innovation of CLIP's architecture is that both the text and image encoders project their respective inputs into a shared embedding space or a lower-dimensional latent space.
	- The dimensionality of this shared space is consistent (e.g., 512).
	- The goal of training is to align the representations of semantically related images and text closely within this shared space11 .... Conversely, representations of dissimilar images and text are pushed far apart.
	- This shared space enables direct comparison between image and text embeddings, for example, using cosine similarity or dot product (if the embeddings are normalized).

- Contrastive Learning: The architecture is specifically designed to facilitate contrastive learning during the pre-training phase18 .... By learning to distinguish between matching and non-matching image-text pairs, the model develops robust embeddings that capture the semantic relationships between the two modalities18 .


- INFERENCE:
![[Attachments/Pasted image 20250403154801.png]]


### Loss:

![[Attachments/Pasted image 20250404155943.png]]