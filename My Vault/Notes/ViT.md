
# ViT


DATE:  25-03-25


Tags: [[Notes/Transformers|Transformers]]  [[CNN]]

# References:

https://www.youtube.com/watch?v=DVoHvmww2lQ


# Content:

### ViT Architecture:

- Image Patch Partitioning: The initial step in the ViT architecture is to divide the input image into fixed-size, non-overlapping patches5 .... For example, a 224x224 pixel image might be split into 196 patches of 16x16 pixels each6 . These patches are treated as the basic units of processing, analogous to words or tokens in NLP.
- Flattening Patches: Each of these two-dimensional image patches is then flattened into a one-dimensional vector5 .... This is done by concatenating the pixel values within the patch, including the color channels (e.g., RGB)6 . This flattening allows the image data to be processed as a sequence, similar to text in a Transformer model.
- Linear Projection (Patch Embedding): The flattened patch vectors are then passed through a linear layer to create lower-dimensional linear embeddings of a fixed size5 .... This projection reduces the dimensionality of the patch vectors while aiming to preserve important visual information5 . This step is analogous to creating word embeddings in NLP.
- Positional Embeddings: Since the Transformer architecture itself is permutation invariant and doesn't inherently understand the order or spatial arrangement of the input sequence, positional embeddings are added to the patch embeddings5 .... These embeddings encode information about the location of each patch within the original image5 .... Positional embeddings are often learned during training as trainable vectors5 ... or can be fixed (e.g., sinusoidal)17 . This allows the ViT to understand the spatial structure of the image6 .... Fine-tuning at higher resolutions requires 2D interpolation of the pre-trained position embeddings because they are modeled with trainable linear layers.
- Transformer Encoder: The sequence of patch embeddings combined with positional embeddings is fed into a standard Transformer encoder5 .... This is the core processing unit of the ViT architecture and consists of multiple layers of Transformer blocks22 .... Each Transformer block typically contains two main sub-layers.
- Multi-Head Self-Attention (MHSA) Layer: This layer allows the model to attend to different parts of the image simultaneously10 .... By computing attention weights, the model determines the importance of each patch relative to all other patches in the sequence10 .... Multi-head attention enhances this by allowing the model to look at the image from several different perspectives and capture both local and global relationships between patches19 .... This enables the ViT to understand the full context of the image and capture long-range dependencies10 .... The attention distance in ViTs can increase with network depth, similar to the receptive field in CNNs.
- Multi-Layer Perceptron (MLP) Layer: After the attention mechanism, the output is passed through a feed-forward network (MLP)5 .... This MLP typically consists of two fully connected (linear transformation) layers with a non-linear activation function (like GELU) in between19 .... The MLP further processes the features extracted by the attention mechanism.
- Layer Normalization (LN) and Residual Connections: These techniques are commonly used within each Transformer block to stabilize training and improve the flow of information through the network5 .... Layer Normalization is applied before each block to scale and center the data5 .... Residual connections (skip connections) allow the model to bypass some layers, aiding in training deeper networks.
- Classification Head: To perform image classification, a classification token ([CLS]) is often prepended to the sequence of patch embeddings before being fed into the Transformer encoder5 .... This special token aggregates information from all the patches as it passes through the encoder layers22 .... The final representation of this [CLS] token is then passed through a classification head, which is typically a shallow MLP or a single linear layer followed by a softmax function to produce the class probabilities5 .... Some ViT variants might use global average pooling (GAP) over the output embeddings of all the patches instead of a [CLS] token to obtain a single image representation for classification22 .... Multihead attention pooling (MAP) is another approach that applies a multi-headed attention block for pooling.


### Variations and Hybrid Architectures:

The original ViT architecture has been the foundation for numerous variations and improvements41 .... Some sources mention hybrid models that apply a ResNet (CNN) before the Transformer to extract feature maps, which are then used to generate the input sequence for the ViT34 .... This can leverage the strengths of CNNs in early feature extraction. Other architectural improvements focus on the attention mechanism itself (e.g., Swin Transformer's shifted windows) or different pooling strategies42 ....

### ViT Architecture in the Larger Context:

The ViT architecture represents a fundamental shift in how images are processed in deep learning. By treating images as sequences of patches, it allows the powerful self-attention mechanisms of the Transformer, originally designed for sequential data like text, to be applied to visual tasks2 .... This enables ViTs to capture global contextual information and long-range dependencies in images more effectively than CNNs, which traditionally rely on local convolutional operations10 ....

However, the weaker inductive biases of the ViT architecture compared to CNNs mean that ViTs typically require larger datasets for pre-training to learn these spatial relationships effectively14 .... When trained on sufficiently large datasets (over 14 million images), ViTs have shown the ability to outperform traditional CNNs on various computer vision benchmarks18 ....

The success of the ViT architecture has spurred significant research and development in the field of computer vision, leading to a plethora of new transformer-based models and their application in a wide range of visual tasks beyond image classification, including object detection, image segmentation, and more complex multi-modal applications11 .... The modularity of the Transformer encoder also allows for easy integration with other architectures, facilitating the development of advanced vision-language models26 .

### ViT vs CNNs:

Vision Transformers (ViTs) and Convolutional Neural Networks (CNNs) are two distinct neural network architectures used for computer vision tasks, each with unique approaches to processing visual information. ViTs have emerged as a competitive alternative to CNNs, which have historically been the state-of-the-art in computer vision.

**Architectural Differences:**

- CNNs have an inductive spatial bias built into them through convolutional kernels, focusing on local feature extraction. They process images hierarchically, starting with low-level local information and gradually building up more global structures in deeper layers.
- ViTs, on the other hand, are based on the transformer architecture originally designed for natural language processing (NLP) and adapted for image analysis by treating an image as a sequence of patches. They utilize a self-attention mechanism to capture global dependencies and relationships between these image patches directly from the earliest layers. This allows ViTs to potentially model long-range interactions within images more effectively than CNNs.
- Unlike CNNs that process data sequentially through layers, transformers in ViTs can take all the image patches as input at once, making them non-sequential in processing.

**Feature Extraction:**

- CNNs perform computation-intensive local feature extraction using convolutional layers.
- ViTs convert an image into a sequence of non-overlapping patches, transform each patch into a vector, and process these vectors within a transformer architecture, enabling them to grasp global information beyond local features.

**Performance and Data Requirements:**

- Research suggests that when trained on substantial datasets, ViTs can outperform CNNs across various domains and settings. They have achieved highly competitive performance in benchmarks for image classification, object detection, and semantic image segmentation.
- However, ViTs generally require significantly more computational resources for training compared to CNNs. They also typically need very large datasets to achieve superior performance. On small to medium datasets, CNNs often perform better than ViTs due to the local receptive fields in CNNs being more effective at encoding local information.
- When trained from scratch on a mid-sized dataset like ImageNet, the performance of ViTs can be inferior to similarly sized CNN alternatives like ResNet.

**Strengths of ViTs:**

- **Global Context:** ViTs can capture global relationships in images from the initial layers due to the self-attention mechanism.
- **Scalability and Flexibility:** ViTs offer scalability and flexibility that can surpass CNNs when sufficient data and computational resources are available.
- **Handling Different Relationships Uniformly:** Transformers can handle pixel-to-pixel, pixel-to-object, and object-to-object relationships uniformly.
- **Multimodal Inputs:** ViTs are well-suited for handling multi-modal inputs like images and text in the same model.
- **Robustness against Attacks:** Vision Transformer Models have shown great potential for privacy-preserving image classification and can outperform state-of-the-art methods in terms of robustness against attacks.
- **Preservation of Spatial Information:** ViTs successfully preserve input spatial information.

**Strengths of CNNs:**

- **Local Feature Extraction:** CNNs are well-suited for tasks where local features play a crucial role.
- **Computational Efficiency (for smaller datasets/real-time applications):** CNNs can be more efficient for smaller datasets or real-time applications due to their simpler architecture compared to the self-attention mechanism in ViTs. Their computational complexity often scales more efficiently with increasing image resolution.
- **Well-Established:** CNNs have been the workhorses of image processing for years, with a wealth of knowledge and established architectures like U-Net.

**Weaknesses of ViTs:**

- **High Computational Cost:** ViTs generally demand significant computational resources for training due to the self-attention mechanism.
- **Data Dependency:** They heavily rely on massive amounts of data for large-scale training to achieve optimal performance.
- **Weaker Inductive Bias:** Compared to CNNs, ViTs have a generally weaker inductive bias, leading to increased reliance on model regularization or data augmentation when training on smaller datasets.
- **Performance on Dense Prediction Tasks:** ViTs seem to work less well for dense prediction tasks like segmentation without certain modifications.

**Weaknesses of CNNs:**

- **Limited Global Context:** CNNs might struggle with capturing global dependencies in larger images as effectively as ViTs.

**Applications:**

- Both ViTs and CNNs are used in various computer vision tasks, including image classification, object detection, image segmentation, and action recognition.
- ViTs have also found applications in generative modeling, multimodal tasks like visual grounding and visual question answering, anomaly detection, autonomous driving, video processing (forecasting, activity recognition), image enhancement, and 3D analysis.
- For specific tasks like medical image segmentation, CNNs, particularly U-Net, have historically performed better, although models like MedSAM (using ViT) are being explored.

**Optimization and Hardware:**

- Model compression techniques like pruning and quantization are reportedly more effective for ViTs compared to CNNs, which is important for deploying large models on edge devices.
- There is growing interest in developing specialized hardware accelerators for transformer architectures like ViTs, such as SwiftTron.

**Interpretability:**

- The self-attention mechanism in ViTs allows for the visualization of attention maps, which can provide insights into which parts of the image the model focuses on, potentially aiding in understanding and improving the model. This process is considered harder for CNN-based models.


### Training:

Dataset Requirements and Pre-training:

- ViTs typically require large datasets for optimal performance1 .... Unlike Convolutional Neural Networks (CNNs), which have inductive biases like locality and translation invariance that allow them to learn effectively from smaller datasets, ViTs have weaker inductive biases and rely more on learning spatial relationships from scratch through the self-attention mechanism.
- ViTs are often pre-trained on very large datasets such as ImageNet11 ... and ImageNet-21k12 ... using supervised learning with image labels14 .... This pre-training phase is essential for the ViT to learn general visual representations.
- Pre-training on large datasets allows ViTs to outperform CNNs, particularly in image classification tasks1 .... However, if the training dataset is small, CNNs generally perform better than Transformers due to their inherent inductive biases.
- The pre-training of ViTs can involve different strategies. The original ViT used fully supervised learning on large image datasets. Self-supervised learning approaches like Masked Autoencoders (MAE)21 ... and BEiT being used for pre-training ViTs, which involve reconstructing masked parts of the input image. DINO24 and DINOv225 are also mentioned as self-supervised vision transformers learning robust visual features without supervision.
- The pre-training objective of ViT is often based solely on **classification**, unlike models like BERT used in NLP, which employ **masked language modeling(MLM)**.

Fine-tuning:

- After pre-training on a large dataset, ViTs are typically fine-tuned on smaller, downstream datasets for specific tasks like image classification, object detection, or segmentation.
- The fine-tuning process often involves discarding the original prediction head (MLP head) and attaching a new linear layer with dimensions corresponding to the number of classes in the new dataset.
- Authors of the original ViT paper found it better to fine-tune at higher resolutions than pre-training. To achieve this, 2D interpolation of the pre-trained positional embeddings is performed, as these embeddings are modeled with trainable linear layers and need to align with the new patch grid size at a higher resolution8 ....

Optimization and Hyperparameters:

- The performance of a ViT model is highly dependent on choices such as the optimizer, network depth, and dataset-specific hyperparameters.
- ViT is more sensitive to the choice of the optimizer, hyperparameters, and network depth compared to CNNs.
- Hyperparameter fine-tuning is a crucial step in optimizing the performance of a ViT model29 . This involves adjusting parameters like the learning rate, batch size, number of layers, and attention heads to achieve the best possible performance on a given task29 . Techniques like grid search, random search, or Bayesian optimization can be used to explore the hyperparameter space.
- Regularization techniques such as dropout or weight decay can be applied during training to prevent overfitting, especially when fine-tuning on smaller datasets.
- Early stopping based on the model's performance on a validation set is also a common technique used during training to prevent overfitting and optimize the training process.
- Preprocessing steps like resizing and normalizing images, data augmentation, and data splitting into training, validation, and test sets are essential for preparing the input data and ensuring stable and effective training of ViT models32 .

Challenges and Specific Techniques:

- One of the challenges of ViTs is their computational cost, particularly due to the quadratic complexity of the attention mechanism with respect to the sequence length (number of patches.
- The lack of strong inductive biases can be seen as both a strength (allowing for learning more general representations) and a challenge (requiring more data and potentially leading to difficulties in generalization with limited data).
- Some sources mention that preprocessing with a layer of smaller-size, overlapping convolutional filters can help improve the performance and stability of ViTs28 . This can be seen as a way to incorporate some inductive bias from CNNs in the initial stages.
- Techniques like layer normalization and residual connections are crucial for stabilizing the training of deep Transformer networks, including ViTs.