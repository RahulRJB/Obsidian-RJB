
# t-SNE


DATE:  09-10-24


Tags: [[Notes/Dimensionality Reduction|Dimensionality Reduction]]

# References:

https://www.youtube.com/watch?v=NEaUSP4YerM


# Content:
- Best works, if we want to reduce the data to 2/3 dims. Beyond that very computationally expensive
- t takes a high dimensional data set and

reduces it to a low dimensional graph

that retains a lot of the original

information
- ![[Attachments/Pasted image 20241009131728.png]]
	- ![[Attachments/Pasted image 20241009131817.png]]
	- ![[Attachments/Pasted image 20241009131846.png]]
	- t-SNE moves these points a little bit at a time until it has clustered them.
	- ![[Attachments/Pasted image 20241009132012.png]]Red point wants to move towards other red points as part of same cluster, but also further away from orange and blue points.
	- ![[Attachments/Pasted image 20241009132130.png]]
	- The Details:
		- Step 1: Determine high-dimensional similarities.![[Attachments/Pasted image 20241009132321.png]]![[Attachments/Pasted image 20241009132345.png]]Do this for all points!
		- ![[Attachments/Pasted image 20241009132434.png]]
		- We them rescale the unscaled similarilities so that they add upto 1.![[Attachments/Pasted image 20241009132643.png]]
		- ![[Attachments/Pasted image 20241009132805.png]]
		- We do the same thing in the loew dimension for each points:![[Attachments/Pasted image 20241009132931.png]]
		- Objective:![[Attachments/Pasted image 20241009132954.png]]





