

# ANNOY(Approximate Nearest Neighbors Oh Yea)


DATE:  20-09-24


Tags:  [[Vector Search]]

# References:

https://www.youtube.com/watch?v=DRbjpuqOsjk&list=WL&index=93


# Content:

## Issues with KNN:
- For a large dataset with millions of record and you want to find complexity for finding K NN to a given point, is O(N). As we double the datapoints, for each datapoint we need to find its distance from the given point 'X' so time also doubles, so O(N).
- Also dependent on dimensions and K.

## Goal of ANN:
- What if we got almost closest nearest neighbour. Sacrificing accuracy for speed.

## ANNOY:
- Designed by Spotify.
- ![[Attachments/Pasted image 20240920022119.png]]
	- We assume to just have 16 data points in 2-dimensional space. Ofcourse if you're using this you're going to have a lot of data, that's the whole reason you'd use it.
	- We start with picking 2 data points at random, let's say we pick 1 and 9. This is represented by this top layer of the tree. This tree structure is going to be crucial to understanding how this approximate nearest neighbor algorithm works.
	- The first node says that i picked 1 and 9 randomly, we then draw a line which is equidistant from those 2 points and that is this blue line here. This line effectively separates the space of points into two pieces.
	- ![[Attachments/Pasted image 20240920022235.png]]We adopt the convention that anytime we take the left branch of the tree, we are looking at stuff below the separating line, and anytime you take the right branch you're looking at stuff above the separating line.
	- We're going to keep splitting till a certain threshold, say each cluster must have a min of 3 points.
	- The whole bottom part of blue line is one region and we have 5 data points there so that's our signal to keep going so let's say we split again and now it just proceeds just as we did before we pick another two random data points within this region. We keep doing this throughout the dataset.
	- After we've done this whole method, we have the tree that you see on the right hand side.![[Attachments/Pasted image 20240920022607.png]]



- ![[Attachments/Pasted image 20240920022814.png]]So how it helps us in finding approx NN???
	- let's add a new data point. Ask who is the closest neighbor to me if i was using original k nearest neighbors i would have to basically just scan through all 16 of these data points compute the distances to find out what the correct answer is.
	- But if we use this structure to do that in a much more efficient way. We just traverse the tree. So start at the top of the tree and ask is this new data point here above or below the blue line and it's below the blue line so i travel down the left side of the tree and we go on till we reach the end i.e a particular region this new point belongs to. This is the crux of the approximate nearest neighbors algorithm. This region which consists of only points 4 5 and 8, we only need to check distances between my mystery point and these three points here and then i find that the closest neighbor would be 8.
	- What we're doing intuitively is that when we constructed this tree we did it in a random fashion but we've created these regions in space such that points that are near each other usually appear within the same region. So all i have to do is figure out which region the mystery point is in and then only search for neighbors within that region because that's going to be some kind of localized pocket of space and in some sense i should be safe to only search that region

- ## Drawbacks 
	- ![[Attachments/Pasted image 20240920022917.png]]The reason this called approximate is that there are places that we can find the not correct answer. For eg. if we have a data point here right below that orange line now the true answer for who's your closest neighbor is obviously 6, but 9 is also kind of right and we trade the accuracy for speed! If you have like 1 million, 10 million data points, we're okay with a little bit of error. 

- ### Considerations:
	- ![[Attachments/Pasted image 20240920023332.png]]
	- we've constructed here is basically a binary search tree. We're basically cutting the size of the problem in half approximately with every branch we go down. So we have complexity of O(log n).
	- With 1 tree we could have gotten really unlucky we could have gotten pretty bad splits since we did this fully randomly and so  instead we construct a forest. Being collections of trees we're averaging over lots of different combinations for what could have happened instead of just 1. So we get better overall performance.
	- Of course one of the other drawbacks would be that when we have a forest of let's say 100 of these search trees you have to obviously store them somewhere so this approximate nearest neighbors does require some additional data storage.


