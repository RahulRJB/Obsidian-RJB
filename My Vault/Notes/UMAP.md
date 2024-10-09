
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
- ![[Attachments/Pasted image 20241009012750.png]]The shape of this curve depends on the number of high-dim neighbors that you want each point to have. A common default is 15, but in this example where we have a tiny dataset and we know each cluster has 3 points, we'll set it to 3. The number of neighbors we want each point to have includes the point itself. Relative low value for the num of neighbors result in small independent clusters in some ways this is sort of like seeing the details but not the big picture. In contrast a relatively large value for the number of neighbors give you more of the big picture and less of the details.
- ![[Attachments/Pasted image 20241009013328.png]]![[Attachments/Pasted image 20241009013358.png]]UMAP uses this log2(num. neighbours) to define the shape of the curve. It is shaped in such a way that the y-axis coordinates for the nearest Neighbors which in this case are points B and C add up log2(num. neighbours), which in this case is 1.6. 
- We do the same thing again, calculate the similarity score relative to point B and C.![[Attachments/Pasted image 20241009023523.png]]![[Attachments/Pasted image 20241009023612.png]]
- ![[Attachments/Pasted image 20241009023655.png]]![[Attachments/Pasted image 20241009023708.png]]
- ![[Attachments/Pasted image 20241009025221.png]]
- Using different curves mean the similarity scores are not symmetrical![[Attachments/Pasted image 20241009025325.png]]
- ![[Attachments/Pasted image 20241009025424.png]]
- **Getting started with the low-dimensional graph**: UMAP initializes a low-dimensional graph, but this low-dim graph may not be ideal because point B needs to be closer to A and further from F. Now to make this low-dimensional graph show the same clusters we see in the high-dimensional data, UMAP picks 2 low-dimensional points that it should move closer together, by randomly selecting a pair of points in a cluster proportionally to their high-dimensional score:![[Attachments/Pasted image 20241009030001.png]]
- So now UMAP randomly selects points A and B which means it wants to move points A and B closer together. UMAP flips a coin and decides to move point B closer to A. Now it picks a point that B should move further from, so UMAP randomly picks a point not in B's High dimensional cluster. Unlike before this time the high dimensional scores do not influence which point is picked. If we imagine UMAP picked E. So finally B has to move towards A and away from E. It has to figure out how much it should move point B.![[Attachments/Pasted image 20241009030316.png]]
- Instead of using a variety of Curves like we did for the high dim data, the low dim similarity scores come from a fixed bell-shaped curve that is derived from a t-distribution.  Because points A and B are in the same cluster or neighborhood, UMAP wants to move  B closer to A in order to maximize this low-dimensional score. In contrast because points B and E are in different clusters UMAP wants to move point B further from E so that it can minimize this low dimensional score. UMAP only moves point B a small amount because when there is a ton of data taking small steps each turn makes it easier to get the low-dimensional graph looking just right.![[Attachments/Pasted image 20241009031707.png]]
- It does the same with all the other points! UMAP keeps moving the points one step at a time until we have 2 low-dimensional clusters that are relatively far from each other just like we see in the high dimensional data.
- **Comparison with t-SNE**: 
	- t-SNE always starts with a random initialization of the low-dimensional graph.  In contrast UMAP uses something called spectral embedding to initialize the low-dimensional graph and what that means is that every time you use UMAP on a specific dataset you always start with the exact same low dimensional graph.
	- t-SNE moves every single point a little bit each iteration, in contrast UMAP can move just one point or a small subset of points each time and this helps it scale well with super big datasets.


