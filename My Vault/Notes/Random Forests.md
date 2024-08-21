
# Random Forests


DATE:  18-08-24


Tags: [[Notes/Statistical ML|Statistical ML]] [[Bagging]]

# References:

[Random Forests Part 1 - Building, Using and Evaluating](https://www.youtube.com/watch?v=J4Wdy0Wc_xQ&list=PLblh5JKOoLUIE96dI3U7oxHaCAbZgfhHk&index=1)


# Content:

- Decision trees are easy to build, easy to use and easy to interpret, but in practice they are not that awesome. To quote from the Elements of Statistical learning: Trees have one aspect that prevents them from being the ideal tool for predictive learning, accuracy. In other words, they work great with the data used to create them but they are not flexible when it comes to classifying new samples. Good news is that random forests combine the simplicity of decision trees with flexibility resulting in a vast improvement in accuracy.

### How to  create Random Forest?

- Step 1: Create a bootstrapped dataset. Randomly select samples from the original dataset with replacement.![[Attachments/Pasted image 20240818222449.png]]
- Step 2: Create a decision tree using the bootstrap dataset but only use a random subset of variables or columns at each step/node. Thus instead of considering all four variables to figure out how to split the root node, we randomly select 2. Same for all the nodes. We just build the tree as usual, but only considering a random subset of variables at each step.
- Step 3: Goto Step1 and repeat. Do this 100s of times.
- ![[Attachments/Pasted image 20240819021543.png]]
- To predict for a given data, we run the data down all the trees we made and check which option received most votes.
- **B**ootstrapping the data plus using the **agg**regate to make a decision is called **Bagging**.

### Evaluating a Random Forest:

- We allow duplicates data in trees in the bootstrapped dataset. As a result some entries are not included in individual trees. Typically about 1/3 of the original data does not end up in the bootstrap datasets. This is called the **out-of-bag** dataset.
- Since the out-of-bag data for a tree was not used to create that tree, we can run it through and see if the tree correctly classifies the sample. Similarly we can run this OOB sample through all of the other trees that were built without it and then do the same thing for all of the other out of bag samples for all of the trees. Ultimately we can measure how accurate our random forest is by the proportion of out-of-bag samples that were correctly classified by the random forest. This is know as the OOB score. The proportion of out-of-bag samples that were incorrectly classified is the out of bag error.
- We compare this OOB score for a random forest built using only 2 variables per step to a random forest built using 3 variables per step. Similarly we test a bunch of different settings and choose the random forest with the highest OOB score.

### Missing Data:

- ![[Attachments/Pasted image 20240819023128.png]]
- ![[Attachments/Pasted image 20240819023245.png]]![[Attachments/Pasted image 20240819023317.png]]Among the people that do not have heart disease, **NO** is the most common value for blocked arteries. So **NO** is our initial guess.![[Attachments/Pasted image 20240819023523.png]]
- Now we refine the guess:
	- Step1: Build a RF.
	   Step2: Run all of the data down all of the trees.
	- Step3: Create a proximity matrix. Row 3 and 4 ended up at the same leaf node that means they're similar. This is how similarity is defined in random forest. Proximity matrix to quantify similarity among the various datapoints. Similarity in RF is determined by if the 2 samples end up in the same leaf node. This is how our proximity matrix looks after running the samples down the 1st tree. ![[Attachments/Pasted image 20240819023710.png]]![[Attachments/Pasted image 20240819024235.png]]
	- Proximity matrix after 2nd tree:![[Attachments/Pasted image 20240819024301.png]]
	- ![[Attachments/Pasted image 20240819024324.png]]![[Attachments/Pasted image 20240819024340.png]]
	- Now we use the proximity values for missing rows to make better guesses about the missing data.![[Attachments/Pasted image 20240819024726.png]]![[Attachments/Pasted image 20240819024806.png]]
	- ![[Attachments/Pasted image 20240819024904.png]]
	- Now that we have refined the guess, we repeat the process all over again.![[Attachments/Pasted image 20240819025024.png]]
### Sample Cluster:

- Using proximity matrix, we can cluster the data. 1 in proximity matrix means the samples are as close as close can be, i.e (1-proximity values) = Distance.![[Attachments/Pasted image 20240819025344.png]]
- So this is super cool because it means that no matter what the data is, ranks, multiple-choice, numeric etc, if we can use it to make a tree we can draw a heat map or an MDS plot to show how the samples are related to each other.

