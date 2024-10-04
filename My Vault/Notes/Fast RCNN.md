
# Fast RCNN


DATE:  04-10-24


Tags: [[Notes/Object Detection|Object Detection]]

# References:
https://www.youtube.com/watch?v=5gAq6BZ87aA




# Content:

- Girshick in 2015 introduced Fast RCNN to enhance the speed and efficiency of the [[RCNN]] model. Instead of treating object detection as 3 separate tasks, Fast R-CNN unifies them into a single framework. This means that instead of independently training models for feature extraction, object classification, and bounding box regression, Fast R-CNN combines them into one cohesive system.
- The CNN module extracts features from the diff ROIs of the same image,  logically these region images are part of our input image and it shouldn't make any difference if we give the whole input to our CNN module because it should again extract the features that it extracts from each region images. So this duty can be done only one time per image, it runs the feature extractor just once on the entire image in a single forward pass and extracts all the features needed for object detection from the whole image in one go. 
- ![[Attachments/Pasted image 20241004014308.png]]So we pass input image through a CNN module called backbone CNN. It extracts all feature in the image and produces image features. We Run The Selective search and it generates region proposals on these image features. We crop and warp the proposed regions and finally we pass each of them to another module which is just a shallow low weight CNN module or even just fully connected layers which are not computationally expensive and it again generates two outputs.
- ![[Attachments/Pasted image 20241004014641.png]]In RCNN, we use this whole module(maybe [[ResNet]], [[AlexNet]]) after cropping and warping the proposed regions.
- ![[Attachments/Pasted image 20241004014821.png]]In Fast RCNN, backbone is computed only once for the whole image and only the shallow part of the end is used for per region Network.
- How to make sure this part is differentiable?
  In RCNN, we use the cropping and warping at first before feeding to CNN module, but here we have CNN module before that, which we need to back propagate and tune its weight. sadly cropping like usual is not differentiable and we cannot compute its gradient. We use ROI pooling.
- ### ROI Pooling:
	- ![[Attachments/Pasted image 20241004015908.png]]
- ### Training the model:
	- ![[Attachments/Pasted image 20241004020202.png]]
	- ![[Attachments/Pasted image 20241004020610.png]]
- ### Finetuning for detection:
	- ![[Attachments/Pasted image 20241004020845.png]]![[Attachments/Pasted image 20241004021038.png]]How we sample region proposals out of all of the possible outputs given by selective search? This is done using mini batch sampling. We use only 2 images for our mini batches. The first mini batch, we pass them through backbone CNN and it gives us the image features. We Run The Selective search algorithm and it outputs ~2000 region proposals. We only selects 64 ROIs from each image.
- ### Multi_task Loss:
	- ![[Attachments/Pasted image 20241004021600.png]]Vector *p* is a discrete probability distribution for our classification problem. We have K+1 categories.
	  Next output layer is our bounding box regression output. We have a separate output for each object we have in the image and also it is not what is produced directly by our output layer and there is a transformation applied before to tweak the bbsproduced by selective search a little bit and produce these vector.
	  Vector *u* which has the exact shape of vector p.
	  Vector *v* which is for the bounding box regression Target.
	- Loss function:![[Attachments/Pasted image 20241004023327.png]]
- ### Scale Invariance:
	- ![[Attachments/Pasted image 20241004023549.png]]Brute force: Use just a single image
	  Image pyramid: use 4 -5 versions of the image of diff sizes, diff scaling. Here training time also increase 4-5 times.



