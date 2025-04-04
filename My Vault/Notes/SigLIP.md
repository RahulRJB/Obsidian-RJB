
# SigLIP


DATE:  04-04-25


Tags:  [[Notes/LLMs|LLMs]] [[Notes/CLIP|CLIP]] [[Multimodal]] 

# References:

https://www.alphaxiv.org/abs/2303.15343


# Content:

### Architecture

SigLIP's model architecture shares similarities with CLIP (Contrastive Language-Image Pre-training), but with key differences, particularly in the loss function.

- Overall Framework Similar to CLIP: SigLIP, developed by Google, follows a framework akin to CLIP, which is a groundbreaking multimodal model that bridges computer vision and natural language processing by learning a shared representation space for images and text1 .... Both models utilize an image encoder and a text encoder.
- Image Encoder: SigLIP employs a [[Notes/ViT|ViT]] encoder for processing images2 . This means that input images are divided into patches, which are then linearly embedded into vectors2 . The authors of the original SigLIP paper used moderately-sized models, such as a B/16 Vision Transformer (ViT) for image embeddings9 . SigLIP 2 also builds upon this with improvements3 . For example, SigLIP 2 can use different Vision Transformer architectures and introduces the NaFlex variant for handling dynamic resolutions and aspect ratios. The SoViT-400m architecture, optimized for shape, is mentioned in the context of a SigLIP model variant ("Siglip So400m Patch14 224")
- Text Encoder: Similar to CLIP, SigLIP uses a transformer encoder for processing text8 . This encoder converts input text sequences into dense embeddings8 . The base SigLIP models utilized a B-sized transformer for text embeddings9 . The text is typically tokenized using a vocabulary, such as a 32k vocabulary sentence-piece tokenizer.
- Contrastive Learning Mechanism: Like CLIP, SigLIP uses a contrastive learning mechanism to align the representations of text and images16 .... This involves maximizing the similarity between embeddings of matching image-text pairs and minimizing the similarity between embeddings of non-matching pairs16 .... The key difference lies in how this similarity is used within the loss function.
- **Sigmoid Loss** as a Defining Feature: The most significant architectural difference between SigLIP and CLIP is the replacement of the softmax-based loss in CLIP with a sigmoid-based loss in SigLIP. This sigmoid loss operates independently on each image-text pair and does not require a global view of pairwise similarities for normalization1 .... This architectural choice enables better scaling of batch size and improved performance, especially at smaller batch sizes.
- SigLIP 2 Enhancements: SigLIP 2 introduces several architectural advancements over the original SigLIP.
	- Location Aware Captioners (LocCa) Decoder: SigLIP 2 adds a text decoder alongside the image and text encoders during training. This transformer decoder with cross-attention aims to achieve grounded captioning (creating captions based on object locations) and referring expression prediction (predicting bounding boxes for textually described locations).
	- Improved Fine-Grained Local Semantics: SigLIP 2 incorporates two additional objectives: Global-Local Loss and Masked Prediction Loss. These, along with self-distillation techniques, help improve the model's ability to understand detailed visual features.
	- Better Adaptability to Different Resolutions: SigLIP 2 features two variants to handle resolution changes: a Fixed Resolution Variant and a Dynamic Resolution (NaFlex) Variant. The NaFlex variant allows a single model to work with multiple sequence lengths and maintain the native aspect ratio of images, which is beneficial for tasks sensitive to resolution and aspect ratio.


### Sigmoid Loss Function:

Sigmoid Loss Function is a key innovation of the SigLIP (Sigmoid Loss for Language-Image Pre-training) model, designed to overcome limitations found in models like CLIP, which utilize a Softmax function with Cross Entropy Loss.

- Replaces Softmax Loss: SigLIP replaces the softmax-based loss used in CLIP with a sigmoid-based loss that operates independently on each image-text pair3 .... This is a fundamental difference in the training objective.
- Formula: The Sigmoid Loss function used in SigLIP is represented as: $$L = -\frac{1}{|B|} \sum_{i=1}^{|B|} \sum_{j=1}^{|B|} \log \frac{1}{1 + e^{z_{ij}(-tx_i \cdot y_j + b)}}$$ where:
	- “N” (or “|B|”) is the batch size.
	- “Σ(i=1 to N) Σ(j=1 to N)” sums over the loss for all combinations of image (i) and text (j) pairs.
	- “z_ij” indicates whether the image-text pair is positive (1) or negative (-1).
	- “t” controls the steepness of the sigmoid curve.
	- “xi · yj” (or “x_i · y_j”) measures the similarity between image and text embeddings.
	- “b” shifts the decision boundary of the sigmoid.
	- The term $\log \frac{1}{1 + e^{(\cdot)}}$ maps the similarity to a probability and then to a loss value.

- ![[Attachments/Pasted image 20250404161256.png]]

- Independent Operation: Unlike the Softmax loss in CLIP, which normalizes across all image-text pairs in a batch, the sigmoid loss operates on each pair individually3 .... This means the loss for each pair (positive or negative) is independent of other pairs in the mini-batch.
- Overcoming Limitations of Softmax: The sigmoid loss in SigLIP addresses several limitations of the softmax approach in CLIP.
	- Issues with Similar Pairs: Softmax might not effectively capture the relative distance between very similar image and text embeddings, potentially causing the model to miss subtle differences. Sigmoid loss, by evaluating each pair independently, can handle nuanced comparisons better.
	- Quadratic Memory Complexity: CLIP's softmax normalization requires storing an NxN matrix for all pairwise similarities, leading to quadratic memory complexity. SigLIP avoids this as each pair operates independently, reducing computational overhead and memory usage.
	- Batch Dependency: Softmax loss is batch-dependent, requiring large and diverse batch sizes for effective training. Sigmoid loss is more batch-agnostic and performs well even with smaller batch sizes
- Benefits of Sigmoid Loss: The use of sigmoid loss in SigLIP leads to several advantages.
	- Improved Performance: SigLIP achieves better performance than CLIP in zero-shot classification accuracy on ImageNet, as well as in image-text retrieval tasks9 .... SigLIP 2 further outperforms previous SigLIP versions in these areas.
	- Efficient Training: Sigmoid loss enables training with larger batch sizes due to its memory efficiency8 .... It also performs well with smaller batch sizes, making it more versatile for different computational resources.
	- More Stable Learning: The sigmoid approach is described as providing a smoother and more stable learning process compared to the contrastive loss used in CLIP.
- Application in SigLIP: SigLIP utilizes the sigmoid loss to train its image and text encoders. The model learns to predict whether an image and text pair are related or not on an individual basis5 .... During inference for tasks like zero-shot image classification, the similarity scores (logits) between image embeddings and text embeddings of the labels are passed through a sigmoid activation function to obtain probabilities.