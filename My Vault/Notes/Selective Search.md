
# Selective Search


DATE:  03-10-24


Tags: [[RCNN]]

# References:




# Content:

Selective Search is a greedy algorithm for region proposals used in RCNN. It combines smaller segmented regions to generate region proposals. This algorithm takes an image as input and output generates region proposals on it. This algorithm has the advantage over random proposal generation in that it limits the number of proposals to approximately _2000_ and these region proposals have a high recall.
### **Algorithm**

1. Generate initial sub-segmentation of the input image.
2. Combine similar bounding boxes into larger ones recursively. Similarities are considered based on color similarity, texture similarity, region size,
3. Use these larger boxes to generate region proposals for object detection.

![[Attachments/Pasted image 20241003233024.png]]



