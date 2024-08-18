
# CART


DATE:  18-08-24


Tags: [[Statistical ML]]

# References:

[Decision and Classification Trees, Clearly Explained!!!](https://www.youtube.com/watch?v=_L39rN6gz7Y)
[Regression Trees, Clearly Explained!!!](https://www.youtube.com/watch?v=g9c66TUylZ4&list=PLblh5JKOoLUKAtDViTvRGFpphEc24M-QH&index=3)
[How to Prune Regression Trees](https://www.youtube.com/watch?v=D0efHEJsfHo&list=PLblh5JKOoLUKAtDViTvRGFpphEc24M-QH&index=4)
# Content:

- Top node called root node or just the root. Rest are called internal nodes or branches. Branches have arrows pointing to them away from them. Lastly end nodes are called leaf nodes or just leaves. Leaves have arrows pointing to them but there are no arrows pointing away from them.
## Classification Tree:

- Using individual categorical features, test how well can they segregate the target: ![[Attachments/Pasted image 20240818194059.png]]
- We do this for all the features. The leaves that cannot segregate the target perfectly: Impure leaves![[Attachments/Pasted image 20240818194444.png]]
- To quantify which feature does a better job to segregate the target, i.e to quantify the impurity of the leaves, we use **Gini Impurity**. Other methods as well like **entropy** and **information gain**. However, numerically the methods are all quite similar.![[Attachments/Pasted image 20240818195406.png]]![[Attachments/Pasted image 20240818195455.png]]
- For num features calculating Gini impurity, a bit more involved. First we sort the rows of the num col from lowest value to highest value. Then we calculate the average for all adjacent values. Lastly we calculate the gini impurity taking all the averages as threshold.![[Attachments/Pasted image 20240818195942.png]]
- We compare Gini impurity for all the features. The feature with least impurity chosen as threshold and so on and so forth we go.![[Attachments/Pasted image 20240818200347.png]]![[Attachments/Pasted image 20240818200722.png]]
- We break a node further only if the impurity of the children nodes after branching < than impurity of the parent node. If its not, we dont branch:![[Attachments/Pasted image 20240818202056.png]]
- We can also have created a threshold such that the impurity reduction has to be large enough to make a big difference as a result we end up with simpler trees that are not overfit.
- We continue till all the leaves are pure or we reach the threshold of min no of samples(to prevent overfit). We keep a threshold of the min no of samples in a node required so that it can be further split, to prevent overfitting. If we have <threshold samples, in the node, we convert it to a leaf even if its impure.  

## Regression Trees:

- Mainly used when we have non-linear relation of data with the target.![[Attachments/Pasted image 20240818204954.png]]
- Again for num features, we sort the rows of the num col from lowest value to highest value. Then we calculate the average for all adjacent values. We use each of these avgs as threshold and avgs of the target as the predicted values. To quantify how good the threshold is, we calculate the residuals by taking the diff between the actual and predicted vals. The threshold with the least residual is chosen for branching and so on and so forth.![[Attachments/Pasted image 20240818210500.png]]![[Attachments/Pasted image 20240818211200.png]]![[Attachments/Pasted image 20240818211338.png]]
- To prevent overfitting:![[Attachments/Pasted image 20240818211701.png]]
- ![[Attachments/Pasted image 20240818211812.png]]
- When we have multiple features, both num and cat to predict the num target. We try with all the features and all the thresholds for each feature. For cat features there is only 1 threshold!. The feature giving the least residual is chosen.![[Attachments/Pasted image 20240818212239.png]]![[Attachments/Pasted image 20240818212309.png]]
- ### Pruning Regression Trees:
	- One of the ways to prevent over-fitting a regression tree to the training data is to remove some of the leaves and replace the split with a leaf that is the average of a larger number of observations.![[Attachments/Pasted image 20240818214841.png]]![[Attachments/Pasted image 20240818214815.png]]
	- The main idea behind pruning a regression tree is to prevent overfitting the training data so that the tree will do a better job with the testing data. But how to decide how much to prune?![[Attachments/Pasted image 20240818215047.png]]
	- Cost complexity pruning: 1st step in cost complexity pruning is to calculate the sum of the squared residuals for each tree. SSR is relatively small for the original full-size tree but each time we remove a leaf, the SSR gets larger. However we knew that was going to happen because the whole idea was for the pruned trees to not fit the training data as well as the full sized tree.![[Attachments/Pasted image 20240818215358.png]]![[Attachments/Pasted image 20240818215436.png]]
	- Weakest link pruning works by calculating a tree score that is based on SSR for the tree or subtree and a tree complexity penalty that is a function of the number of leaves or terminal nodes in the tree or subtree. The tree complexity penalty compensates for the difference in the number of leaves. **α** is a tuning parameter that we find using cross-validation.![[Attachments/Pasted image 20240818215955.png]]
	-  **α** =10,000:![[Attachments/Pasted image 20240818220111.png]]
	  **α** =22,000:![[Attachments/Pasted image 20240818220201.png]]
	- To get the value of **α**, start with 0, full tree has the lowest Tree Score, increase **α** till pruning leaves required to get the lowest Tree Score.![[Attachments/Pasted image 20240818220551.png]]
	- We do 10-fold cross-validation and we select the **α** that, on average, gives the best results, i.e, on average gave us the lowest sum of squared residuals with the testing data.

## Missing Values/Feature Selection:
- We can check the features repeatedly used for branching, those are the important features and in turn is one of the way of feature selection.
- We can go down the tree and check the expected missing value for the data knowing the target. SO CART can be used to fill missing data as well. 



