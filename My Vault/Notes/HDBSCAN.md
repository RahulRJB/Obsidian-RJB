
# HDBSCAN


DATE:  09-10-24


Tags: [[Notes/Clustering|Clustering]]


# References:

https://www.youtube.com/watch?v=g5DZuRDnOGY


# Content:
- Density based clustering approach: Rather than just finding compact regions that are compacted together so all the distances are close, it tries to find regions where the data is dense that are separated from other regions by areas where the data is more sparse. so you're looking for these islands of density amidst a gigantic sea.
- It has a hierarchical component , so in a sense if you have regions of different density something like DBSCAN will not pick them out quite so well so when we're talking about islands of density within a sea you can raise or lower the sea level and different Islands will pop up at different sea levels so being able to have a different sea level to pick out Islands in different regions of your data space that's really important and that's the sort of thing that HDBSCAN empowers
- ![[Attachments/Pasted image 20241009122815.png]]
	- Often we don't know apriori what the data should look like and that's where the density-based techniques which are non-parametric are better. They learn the data distribution in that sort of a model in and of themselves so they're much more powerful so that's where DBSCAN, Mean shift are good. 
	- Hierarchical clustering method means you're going to build up a full hierarchy of how different clusters merge together until you get to a single root cluster. A very powerful technique but most of them tend to be more in the centroid/parametric mold. 
	- HDBSCAN is one of those rare algorithms that manages to combine the sort of best of both worlds.
- HDBSCAN tends to like lower dimensional data. **Upto about 50 dims**. This is for any density based approach. This is because notion of density just gets trickier in higher dims, because higher dimensional spaces have in a sense more corners, there's more space to be filled up and so you need ever more data to manage to get the same amount of density in the space and if you're building your whole algorithm out of finding and comparing densities, it means for high dimensional data you would need ridiculously large volumes of data to manage to get enough traction for these algorithms to really bear fruit. 
- But often while the data exists in a high dim space there's some underlying structure to the data that maybe lives in a lower dimensions, but it's very hard to cluster it out of the box just using density-based clustering ambient to that high dimensional space. So we can use dim reduction techniques like UMAP to lower dims and then use the clustering technique.
- Has Noise in the name: Density-based approaches, what we really mean by cluster is a dense region and not all of the data is going to fall in those dense regions. So the ability to point out these outliers/noise in the data and say this doesn't have to belong to any given cluster, is really powerful tool. Whereas something like k-means which basically assigns every data point to a cluster it tends to end up with quite polluted clusters because a whole bunch of noise points that are just sort of a little bit more scattered or random they have to be assigned to some clusters. So DBSCAN and HDBSCAN can handle this noise aspect much more cleanly and they essentially drop away these outliers and just ignore them for the purposes of the clustering and returns clusters that are just dense.
- 



