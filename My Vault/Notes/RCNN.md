
# RCNN


DATE:  04-09-24


Tags: [[Object Detection]]


# References:
- https://www.youtube.com/watch?v=nJzQDpppFj0
- 


# Content:

- [[CNN]] goal: Predict output in an image.
- Object detection models: Predict output in an image + bounding boxes.
- #### Process:
	- The CNN bit used ![[Attachments/Pasted image 20240904135328.png]]
	- For object detection task we have to modify it a little bit. After fully connected layer, we create 2 branches, one for CNN classification, and other, another fully connected layer to Output our bounding box coordinates. We already know what are the correct bounding boxes during training, use [[Notes/L2 loss (MSE loss)]]for the loss function. Lastly we use weighted sum to compute our final loss.![[Attachments/Pasted image 20240904135559.png]]
- 1 major disadvantage, it only can detect one object in the picture because we just output one single bounding box coordinates. Can't use this architecture in scenarios when multiple objects exist in our input. 



this architecture is used for object

localization. whenever they say optic

localization to you they mean only one

object exists in the image



#### Earaly approach:


