
# Gradient Boost


DATE:  19-08-24


Tags: [[Notes/Statistical ML|Statistical ML]],  [[Boosting]] 


# References:

https://www.youtube.com/watch?v=3CC4N4z3GJc&list=PLblh5JKOoLUJjeXUvUE0maghNuY2_5fY6&index=1


# Content:

## Regression:

- Gradient Boost starts by making a single leaf instead of a tree or stump. This leaf represents an initial guess for the weights for all of the samples. When trying to predict a continuous value like weight, the first guess is the average value. 
- Then gradient boost builds a tree like [[AdaBoost]]. This tree is based on the errors made by the previous tree but unlike Adaboost, this tree is usually larger than a stump, that said gradient boost still restricts the size of the tree. Usually the size is restricted to 8 to 32 leaves.
- Like Adaboost gradient boost scales the trees, however gradient boost scales all trees by the same amount.
- GB continues to build trees in this fashion until it has made the number of trees you asked for or additional trees fail to improve the fit.

### Steps:
- ![[Attachments/Pasted image 20240819031411.png]]
- Get he pseudo-residuals using the initial guess.![[Attachments/Pasted image 20240819031534.png]]
- ![[Attachments/Pasted image 20240819031628.png]]![[Attachments/Pasted image 20240819031750.png]]
- We now combine the original leaf with the new tree to make a new prediction of an individual's weight from the training data.![[Attachments/Pasted image 20240819032004.png]]This is awesome, but the model fits the training data too well, in other words we have low bias but probably very high variance. 
- To prevent low bias, high variance,  gradient boost uses a learning rate to scale the contribution from the new tree. The LR is a value between 0 & 1. In this case we'll set the LR=0.1. Now the predicted weight equals 71.2 + (0.1x16.8) = 72.9![[Attachments/Pasted image 20240819032226.png]]With the learning rate=0.1, new prediction isn't as good as it was before but it's a little better than the prediction made with just the original leaf which predicted that all samples to be just avgs. In other words scaling the tree by the learning rate results in a small step in the right direction.
- According to Jerome Freedman, who invented gradient boost, empirical evidence shows that taking lots of small steps in the right direction results in better predictions with a testing data set ie lower variance.
- Now we repeat the steps of creating a new tree with updated residuals.![[Attachments/Pasted image 20240819032826.png]]Original residuals > Ori+1st tree > Ori+1st tree+2nd tree.
  The residuals keep getting smaller, so taken another step in the right direction.
- We keep making trees until we reach the maximum specified or adding additional trees does not significantly reduce the size of the residuals.

### Mathematical details:
- ##placeholder##

## Classification:

- Just like in regression, we start with a leaf that represents an initial prediction for every individual. When we use GB for classification the initial prediction for every individual is the log(odds). I like to think of the log(odds) as the logistic regression equivalent of the avg so let's cal the overall log(odds) that someone loves troll 2, since 4 people love troll 2 and 2 people do not, the log(odds) that someone loves troll 2=log(4/2)=0.7. This is the initial prediction how do we use it for classification, like with logistic regression the easiest way to use the log(odds) for classification is to convert it to a probability and we do that with the logistic function:
  Proba of loving troll 2= e**(log(odds)) / 1+e**(log(odds))
  We get 0.7 as the proba of loving troll 2. Since the probability of loving troll 2 is > 0.5 we can classify everyone in the training dataset as someone who loves troll 2. Note while 0.5 is a very common threshold we could have used a different value.![[Attachments/Pasted image 20240819034401.png]]
- Classifying everyone in the training dataset as loving troll 2 pretty lame because 2 of the people do not love the movie, we can measure how bad the initial pred is by calculating pseudo residuals, the diff bet the observed and the pred values. 
  The predicted proba of loving troll 2 is 0.7 the red dots with the prob of loving troll to equal to zero represent the 2 people that do not love troll 2 and the blue dots with the prob of loving troll 2 equal to 1 represent the four people that love troll 2, in other words the red and blue dots are the observed values and the dotted line is the predicted value of 0.7, so for this sample we plug in 1 for the observed value and 0.7 for the predicted value and we get 0.3 as residual.![[Attachments/Pasted image 20240819034732.png]]
- Now we build a tree using Likes popcorn, Age and Favorite Color to predict the residuals. Here as well we are limiting the number of leaves that we will allow in the tree.![[Attachments/Pasted image 20240819035222.png]]
- When we used GB for regression a leaf with a single residual had an output value equal to that residual, in contrast when we use GB for classification the situation is a little more complex. This is because the predictions are in terms of the log(odds) and this leaf is derived from a probability so we can't just add them together and get a new log(odds) prediction without some sort of transformation. ![[Attachments/Pasted image 20240819035419.png]]
- The most common transformation is the following formula: ![[Attachments/Pasted image 20240819035443.png]]![[Attachments/Pasted image 20240819035547.png]]![[Attachments/Pasted image 20240819035620.png]]
- Just like before the new tree is scaled by a LR.![[Attachments/Pasted image 20240819035750.png]]
- So first we obtain log(odds). Use it to predict probability of individual samples. Use this pred probability to get residuals, Create trees using it. Since this trees is made of probabilitiea, use a formula to transform it back to log(odds). Use the initial log(odds) and the tree log(odds) to get updated log(odds). Transform log(odds) back to probability.![[Attachments/Pasted image 20240819040128.png]]
- We repeat this process to create many trees.![[Attachments/Pasted image 20240819040242.png]]![[Attachments/Pasted image 20240819040346.png]]
### Mathematical details:
- ##Placeholder##

