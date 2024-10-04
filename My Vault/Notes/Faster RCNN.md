
# Faster RCNN


DATE:  04-10-24


Tags: [[Notes/Object Detection|Object Detection]]

# References:
https://www.youtube.com/watch?v=auHkGHM-x_M
https://medium.com/@kattarajesh2001/object-detection-part-2-two-stage-detectors-r-cnn-fast-r-cnn-faster-r-cnn-026e5fac5dd0


# Content:
- ![[Attachments/Pasted image 20241004123209.png]]Selective Search is the bottleneck. Also CPU-based algorithms like Selective Search in which no learning is involved, which incurred significant computational overhead and hindered real-time performance. 
- ![[Attachments/Pasted image 20241004123304.png]]To mitigate the problem we replace the Selective search with a CNN module called RPN which basically does the same thing.  It is a convolutional network that generates region proposals directly from the input image. By leveraging the RPN, Faster R-CNN drastically reduced the region proposal time from several seconds to mere milliseconds per image, leading to significant speed improvements. Moreover, by sharing layers between the RPN and subsequent detection stages, Faster R-CNN enhanced feature representation throughout the entire detection pipeline, ultimately improving the accuracy and efficiency of object detection systems.
- ## Backbone:

	The first step in the Faster R-CNN architecture is same as the previous versions utilizing a pretrained CNN, such as VGG or ResNet, to extract informative features from input images, utilizing an intermediate convolutional layer’s output for subsequent object detection tasks. Each convolutional layer in the CNN learns hierarchical features from the input image. Lower layers detect simple features like edges, while deeper layers capture complex patterns and object representations. Through convolution and pooling operations, the input image is transformed into a convolutional feature map with reduced spatial dimensions but increased depth. The convolutional feature map retains important spatial relationships between objects and features. Each location in the map corresponds to a receptive field in the original image, capturing localized information. For example, if the feature map of an image of size 224x224 is 7x7, then each 1x1 location on the feature map is corresponds to a 32x32 sized receptive field at the corresponding location on the image. Despite spatial dimension reduction, the feature map preserves the relative positions of objects and features encoded by the network. While VGG was widely used in earlier implementations of Faster R-CNN, architectures like ResNet have become more common due to their superior performance and efficiency. Despite differences in architecture, both VGG and ResNet serve the same purpose in Faster R-CNN: to extract informative features from input images, which are then used for region proposal and object detection tasks.
	
	![](https://miro.medium.com/v2/resize:fit:630/1*b5gA7W5speHgR1znEB9gdw.gif)
- ### RPN:
	- ![[Attachments/Pasted image 20241004124634.png]]
	- When performing object detection, one of the primary tasks is to predict bounding boxes that tightly enclose the objects of interest in an image. Without any external algorithm like selective search or sliding window to propose regions, how can a neural network alone detects multiple objects given a feature map? Traditionally, one might expect a neural network to directly output the (x, y)-coordinates of these bounding boxes. However, this approach poses several challenges:
		1. Directly predicting bounding box coordinates might lead to values that fall outside the boundaries of the image. This creates difficulties in interpretation and application since bounding boxes must remain within the image limits.
		2. Ensuring that predicted bounding boxes satisfy constraints like **xmin < xmax and ymin < ymax** is essential for valid bounding box predictions. However, directly predicting coordinates doesn’t inherently enforce these constraints, potentially leading to invalid box predictions.
	To address these challenges, Girshick et al. proposed the concept of **anchors** in the Faster R-CNN framework. Anchors are predefined bounding boxes of various sizes and aspect ratios that act as reference boxes across the image. Rather than directly predicting absolute bounding box coordinates, the network learns to predict offsets or adjustments from these anchor boxes. These offsets include dx−center, dy−center, dwidth, and dheight, allowing for adjustments to the anchor boxes to better fit the objects in the image.
	
	![](https://miro.medium.com/v2/resize:fit:576/1*vQrsJkBXBqo3ankVxw3SJA.png)
	
	anchor box of different scales and aspect ratios (3x3=9 boxes)
	
	The goal of generating anchors is to obtain good coverage over all possible scales and sizes of objects in an image. For example a person standing and a car both boxes are having different aspect ratios(1:2 and 2:1). For each point on the output feature map, a set of anchors is generated(**n** aspect rations and **m** scales gives **m*n** anchors). These anchors are placed densely across the spatial dimensions of the feature map, covering various locations(receptive fields) and scales in the input image. If the feature map size is **kxk** and the number of anchor boxes are lets say **p** boxes, then it generates these **p** boxes at each location on the feature map and that gives **k*k*p** anchor boxes per image. The purpose of using anchors is to provide a systematic way of proposing potential object regions without considering all possible bounding box locations. By placing anchors of different sizes and aspect ratios at each location on the feature map, the network can learn to predict whether an object is present at that receptive field on the image and estimate its size.
	
	![](https://miro.medium.com/v2/resize:fit:630/1*EfEdbhUSlUJxctBWHd0zvg.png)
	
	Anchor boxes at each location on the feature map and corresponding receptive field on input image.
	
	So, for each image, RPN generates k*k*p anchor boxes (kxk feature map size, p is the number of anchor boxes per location(number of aspect ratios*number of scales) and now for every anchor we ask two questions-
	
		1. Does the anchor contains any relevant object?
		
		2. If object is present, what are the adjustments to adjust that anchor box to better fit the object?

	So, the RPN predicts the likelihood of each anchor box containing an object (foreground) or background without worrying about what type of object in that box like a pedestrian, car etc. It takes the convolutional feature map from the base network as input and applies convolutional layers to predict two outputs in parallel: foreground probabilities and bounding box adjustments. The foreground probability indicates the likelihood of an anchor box containing an object, while the bounding box adjustments refine the coordinates of the anchor boxes to better fit the objects. Non-maximum suppression is then applied to remove redundant and overlapping proposals, and a proposal selection step is performed to choose a fixed number of top-scoring proposals for further processing. So the region proposal network outputs these adjusted boxes or proposals along with their objectness scores(probability of the box containing object of interest).
	![](https://miro.medium.com/v2/resize:fit:538/1*rhNlFlkTkId6keWxrjvQOQ.jpeg)

## RPN Network Architecture and training:

![](https://miro.medium.com/v2/resize:fit:337/1*2t9LOkMjmzXuY-wtQHxryQ.png)

Region proposal network

1. The RPN takes the feature map obtained from the backbone network as input, applies a 3x3 convolutional layer with 512 units. This convolutional layer operates independently at each spatial location in the feature map, producing a 512-dimensional feature vector or descriptor for every location. This step helps to enhance the representational capacity of the features and extract more discriminative information relevant for object detection.
2. After the convolutional layer, the feature map is fed into two sibling layers. The first sibling is a 1x1 convolutional layer with 18 units, which is responsible for object classification. The output of this layer has a size of (H, W, 18), where H and W represent the dimensions of the feature map and each of the 18 units in this output corresponds to one of the 9 anchors (each anchor has 2 units for objectness score, forground or background). This output provides probabilities indicating whether each anchor at every spatial location contains an object or background.
3. The second sibling layer is another 1x1 convolutional layer, but with 36 units. This layer is responsible for bounding box regression, which aims to refine the coordinates of the anchors to better fit the objects present in the image. The output of this layer has a size of (H, W, 36), where the 36 units corresponds 9 anchors with 4 units for each (9*4=36) . The output provides regression adjustments that adjust the coordinates (x, y, width, height) of the anchors.

**Training:** During training, the RPN aims to effectively classify anchors as foreground (containing objects) or background (not containing objects), and to regress the bounding box coordinates of the anchors to better fit the objects.

1. All anchors are categorized into two groups based on their overlap with ground-truth objects. Anchors with an highest Intersection over Union (IoU) with the ground truth box or greater than 0.7 with any ground-truth object box are labeled as “foreground” (positive anchors), indicating they contain objects. Anchors with an IoU less than 0.3 with all ground-truth objects are labeled as “background” (negative anchors), indicating they do not contain objects. Same ground truth box can cause multiple positive anchors.
2. From the pool of labeled anchors of an image, a mini-batch of size 256 (128 foreground,128 background) is randomly sampled for training. The goal is to maintain a balanced ratio between foreground and background anchors in the mini-batch other wise it causes bais in the training towards negatives. This balanced sampling ensures that the model is exposed to sufficient examples of both classes during training, preventing class imbalance issues. If there are insufficient number of positives, then it pads the remaining in the mini batch with negatives samples.
3. Once the mini-batch is formed, the RPN training loss is calculated two types of losses:

![](https://miro.medium.com/v2/resize:fit:553/1*jvd66qNECwE8MWpId38vIQ.png)

- Binary Classification Loss(_L𝒸ₗₛ(pᵢ, pᵢ*)_): This loss is calculated using binary cross-entropy, which measures the discrepancy between predicted probabilities and ground-truth labels for foreground vs. background classification. All anchors in the mini-batch contribute to this loss calculation.
- Bounding Box Regression Loss(_Lᵣₑ(tᵢ, tᵢ*)_): Only foreground anchors contribute to this loss calculation. For each foreground anchor, the RPN computes the regression target by comparing its coordinates to the closest ground-truth object’s coordinates. The regression loss is then calculated using Smooth L1 loss, which is a modified version of L1 loss that handles outliers better and ensures faster convergence when the errors are small.

During the testing phase of the object detection pipeline the Region Proposal Network (RPN) utilizes a series of post-processing steps and are applied to the 20,000 anchors generated for each image to obtain object proposal bounding boxes. First, the regression coefficients obtained from the RPN are applied to the anchors to refine their positions, resulting in more precise bounding boxes. Next, the bounding boxes are sorted based on their classification scores, and a non-maximum suppression (NMS) algorithm is employed with a threshold of 0.7. This process involves discarding overlapping bounding boxes by retaining only the highest-scoring box within a group of overlapping ones. As a result, approximately 2,000 proposals per image are retained after NMS. Furthermore, any bounding boxes extending beyond the image boundaries are kept intact but clipped to fit within the image boundaries.

During the training of subsequent stages, such as the Fast R-CNN detection pipeline, all 2,000 proposals generated by the RPN are utilized. However, at test time, only the top N proposals with the highest scores are selected for further processing by the Fast R-CNN detector. This selection mechanism helps streamline the detection process by focusing on the most promising object proposals while discarding less relevant ones, ultimately leading to more efficient and accurate object detection.

## 3. Region of Interest (RoI) Pooling:

![](https://miro.medium.com/v2/resize:fit:273/1*rJrfudUnwm_LQstx19qbBw.png)

Once the RPN generates region proposals, just like fast rcnn, they are passed through RoI pooling. RoI pooling extracts fixed-length feature vectors from each region proposal, regardless of their size or aspect ratio. This step ensures that each proposed region is represented by a consistent feature representation, facilitating subsequent processing.

## 4. RCNN Module:

In the final stage of Faster R-CNN, the goal is to classify object proposals into different classes and refine their bounding boxes based on the predicted class. To achieve this, R-CNN module utilizes two different fully-connected layers for each object proposal:

1. **Classification Head:**

- The first fully-connected layer is responsible for classifying the proposals into one of the possible classes, along with a background class for rejecting undesirable proposals.
- It consists of _C_+1 units, where _C_ is the total number of classes, and an additional unit is dedicated to the background class.
- The output of this layer provides the classification scores for each class, including the background class, indicating the likelihood of the proposal belonging to each class.

2. **Regression Head:**

- The second fully-connected layer is designed for bounding box regression, aiming to better adjust the bounding box for the proposal based on the predicted class.
- It consists of 4×(_C_+1) units, where _C_ again represents the total number of classes.
- The output of this layer comprises 4×(_C_+1) values, where each group of four values corresponds to the regression adjustments for one class. These adjustments are applied to refine the bounding box coordinates for each class prediction.

By using these two separate fully-connected layers, R-CNN can simultaneously perform classification and regression tasks for each object proposal, leading to more accurate and robust object detection results. The targets for this stage are calculated similarly to the Region Proposal Network (RPN), but with consideration for multiple classes. Proposals with IoU greater than 0.7 with any ground truth box are assigned to that ground truth class, while those with IoU less than 0.3 are labeled as background. Proposals without any intersection are ignored, assuming the focus is on solving harder cases. Bounding box regression targets are computed as the offset between the proposal and its corresponding ground-truth box for assigned class proposals.

For training, a balanced mini batch of size 64 is sampled, with up to 25% foreground proposals (with class) and 75% background. Classification loss is calculated using multiclass cross-entropy for all selected proposals, while Smooth L1 loss is applied to the 25% proposals matched to a ground truth box. It’s crucial to handle the bounding box regression loss carefully, as the R-CNN’s fully connected network outputs predictions for each class. Hence, when computing the loss, only the prediction for the correct class should be considered. This approach ensures effective training of the R-CNN model for accurate object detection and bounding box refinement.

## Four Step alternte training:

In the four-step training method used by the authors to ensure weight sharing between the Region Proposal Network (RPN) and the detector network in Faster R-CNN, the process unfolds as follows:

a) Independent Training of RPN: The RPN is trained separately, following the standard procedure described earlier. The backbone CNN for this task is initialized with weights pre-trained on an ImageNet classification task, and then fine-tuned specifically for the region proposal task. This step allows the RPN to learn to propose regions of interest effectively.

b) Independent Training of Fast R-CNN Detector: Similarly, the Fast R-CNN detector network is trained independently. The backbone CNN is initialized with weights from a network pre-trained on ImageNet for classification and then fine-tuned for the object detection task. During this stage, the weights of the RPN are kept fixed, and the proposals generated by the RPN are used to train the Fast R-CNN. This phase enables the detector network to learn object classification and bounding box regression tasks.

c) Fine-tuning RPN with Fast R-CNN Weights: In this step, the RPN is re-initialized with weights from the trained Fast R-CNN network. The RPN is then fine-tuned for the region proposal task. However, only the layers unique to the RPN are fine-tuned, while the weights of the common layers shared with the detector network are kept fixed. This ensures that the RPN learns to generate region proposals using the features learned by the detector network.

d) Fine-tuning Fast R-CNN Detector with Updated RPN: Finally, using the newly fine-tuned RPN, the Fast R-CNN detector network is fine-tuned again. Similar to the previous step, only the layers unique to the detector network are fine-tuned, while the common layer weights shared with the RPN remain fixed. This step allows the detector network to adapt to the updated region proposals generated by the refined RPN while preserving the shared feature representation learned during previous training stages.




