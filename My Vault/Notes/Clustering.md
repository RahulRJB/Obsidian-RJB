
# Clustering


DATE:  09-10-24


Tags:

# References:




# Content:
- ![[Attachments/Pasted image 20241009122757.png]]
	- Top-left: Parametric means you have some model of what your clusters should look like. Like GMM assumes that your clusters should look like Gaussian distributions. So if you know what your data clusters should look like, if you can build a statistical model of them that's a fine approach.
	- But often you don't know apriori what your data should look like and that's where the density-based techniques which are non-parametric are better. They learn the data distribution in that sort of a model in and of themselves so they're much more powerful so that's where DBSCAN, Mean shift are good.
	- Hierarchical clustering method means you're going to build up a full hierarchy of how different clusters merge together until you get to a single root cluster. A very powerful technique but most of them tend to be more in the centroid/parametric mold. HDBSCAN is one of those rare algorithms that manages to combine the sort of best of both worlds.




