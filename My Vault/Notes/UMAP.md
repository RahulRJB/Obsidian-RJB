
# UMAP


DATE:  09-10-24


Tags: [[Dimensionality Reduction]]

# References:

https://www.youtube.com/watch?v=eN0wFzBA4Sc


# Content:

- Uniform Manifold Approximation and Projection.
- [[PCA]] Drawbacks: It only works well when PC1&2 account for most of the variation in the data. If we have a really complicated dataset, PCA may not work very well.
- Popular because it is relatively fast even with large data sets and similar samples tend to cluster together in the final output so it is **useful for identifying similarities and outliers**.
- ![[Attachments/Pasted image 20241009012002.png]]
- ![[Attachments/Pasted image 20241009012118.png]]Idea is to initialize the low-dim points and then move the low dimensional points around until they form clusters that have the same relationships we see in the high-dim data, in other words because points A, B and C are clustered together and are relatively far from the other cluster D, E, F, UMAP wants the low dimensional points A, B and C to be close to each other and relatively far from the other cluster.
- It calculates similarity scores to help identify clustered points so it can try to preserve that clustering in the low-dimensional graph.![[Attachments/Pasted image 20241009012520.png]]
- ![[Attachments/Pasted image 20241009012605.png]]
- ![[Attachments/Pasted image 20241009012750.png]]The shape of this curve depends on the number of high-dim neighbors that you want each point to have. A common default is 15, but in this example where we have a tiny dataset and we know each cluster has 3 points, we'll set it to 3. The number of neighbors we want each point to have includes the point itself.
- ![[Attachments/Pasted image 20241009013328.png]]![[Attachments/Pasted image 20241009013358.png]]UMAP uses this log2(num. neighbours) to define

the shape of this curve the curve is

shaped in such a way that the Y AIS

coordinates for the nearest Neighbors

which in this case are points B and C

add up to the log base 2 of the number

of nearest neighbors you specified which

in this case is

1.6 in other words this curve is shaped

the way it is so that the y- AIS

coordinate for B is 1.0 and the y-axis

coordinate for C is

0.6



