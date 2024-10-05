
# HNSW


DATE:  20-09-24


Tags: [[Notes/Vector Search|Vector Search]]

# References:
https://www.youtube.com/watch?v=QvKMwLjdK-s



# Content:

- We can split Approximate Nearest Neighbor algorithms into 3 broad categories:
	- trees 
	- hashes 
	- graphs
- HNSW is in the graph category.
- It's a type of proximity graph which simply means that vertices or nodes which are vectors in our graph, are connected to other nodes or vertices based on their proximity and we would typically measure this proximity using euclidean distance.
- 2 predecessors of HNSW:
	- Prob skip list
	- NSW graphs
- Probability Skip List:![[Attachments/Pasted image 20241006005025.png]]Whole point of this is, when we have a lot of values this can reduce the number of steps we need to take in order to get to our final value.
- NSW graphs: ![[Attachments/Pasted image 20241006005433.png]]This has both short length links and long length links between diff vertices, making it very easy to navigate.
  Have a specific entry point to enter the graph.
  Use greedy routing, i.e. from a vertex to navigate to the vertex closest to the target. If we find our there are no near vertices in our friend list, this is a local minimum and this is our stopping condition we cannot get any closer we're kind of stuck in in that local minimum.
- HNSW:
  ![[Attachments/Pasted image 20241006010528.png]]![[Attachments/Pasted image 20241006010716.png]]

