
# Random Forests


DATE:  18-08-24


Tags: [[Notes/Statistical ML|Statistical ML]]

# References:

[Random Forests Part 1 - Building, Using and Evaluating](https://www.youtube.com/watch?v=J4Wdy0Wc_xQ&list=PLblh5JKOoLUIE96dI3U7oxHaCAbZgfhHk&index=1)


# Content:

- Decision trees are easy to build, easy to use and easy to interpret, but in practice they are not that awesome. To quote from the Elements of Statistical learning: Trees have one aspect that prevents them from being the ideal tool for predictive learning, accuracy. In other words, they work great with the data used to create them but they are not flexible when it comes to classifying new samples. Good news is that random forests combine the simplicity of decision trees with flexibility resulting in a vast improvement in accuracy.

### How to  create Random Forest?

- Step 1: Create a bootstrapped dataset. Randomly select samples from the original dataset with replacement.![[Attachments/Pasted image 20240818222449.png]]
- Step 2: Create a decision tree using the bootstrap dataset but only use a random subset of variables or columns at each step. Thus instead of considering all four variables to figure out how to split the root node, we randomly select 2.




