
# RCNN


DATE:  04-09-24


Tags: [[Object Detection]]


# References:
- https://www.youtube.com/watch?v=nJzQDpppFj0
- 


# Content:

- [[CNN]] goal: Predict output in an image.
- Object detection models: Predict output in an image along with bounding boxes.
- ### Process:
	- The CNN bit used ![[Attachments/Pasted image 20240904135328.png]]
	- For object detection task we have to modify it a little bit. firstly all of the things we do after

fully connected layer is going to get

separated as a branch and what's the

other Branch it's another fully

connected layer to Output our bounding

box coordinates

and again we already know what are the

correct bounding boxes during training

and we use L2 loss for this allowance

function

last but not least we can use weighted

sum to compute our final loss![[Attachments/Pasted image 20240904135559.png]]

one major disadvantage it only can

detect one object in the picture because

we just output one single bounding box

coordinates

so we actually can't use this

architecture in scenarios when multiple

cars exist in our input



this architecture is used for object

localization. whenever they say optic

localization to you they mean only one

object exists in the image



#### Earaly approach:


