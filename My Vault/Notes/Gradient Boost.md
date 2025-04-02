
# Gradient Boost


DATE:  19-08-24


Tags: [[Notes/Statistical ML|Statistical ML]],  [[Boosting]] , [[Notes/GBTs|GBTs]]


# References:

https://www.youtube.com/watch?v=en2bmeB4QUo&t=151s

# Content:

## Why Gradient Boosting?

- CONCEPT:  
![[Attachments/Recording 20250402163008.webm]]


- Set of features x to predict y:![[Attachments/Pasted image 20240821102753.png]]
- Boosting meaning: We learn f(x) by leveraging a sum of m weak learners![[Attachments/Pasted image 20240821102945.png]]
- Think of a weak learners as an underpowered linear regression or SVM or a Decision tree. Only condition is that it should be underpowered it should not be too complicated. More of an art than a science but basically means that you're not trying to kind of capture all the dynamics in your data with just one of these weak learners, you're trying to capture some of them. Idea of boosting is that the next weak learner that you train is going to learn from the mistakes of all the weak learners that came before it so you train some really bad weak learner first, the next one learns from the mistakes of that one, the one after that learns from the mistakes of the first two and so on and so on. At the end of the day although each of them is weak by itself when you combine them together you end up with something that's actually extremely powerful.
- **Step 0:** Define a loss function that's easily differentiable and in an efficient way, hence the name gradient boosting bcs we need to take the gradients of the loss function.![[Attachments/Pasted image 20240821103723.png]]
- **Step 1:** Start with some extremely weak learner f1(x) and for example we can just take the mean of the number of ice cream cones we've sold in our training data and that can just be our first model. Clearly a terrible idea to just predict the mean but it's starting somewhere. Clearly better function would have given us a much lesser loss![[Attachments/Pasted image 20240821104148.png]]
- **Step 2:** Claculate gradients for all data points for the 1st function.![[Attachments/Pasted image 20240821104507.png]]Why did we do this? Did this because we would like the next week learner that we learn to hopefully move this prediction in the direction of the decreasing loss.
- **Step 3:**  Fit that next week learner, learning from the mistakes of the old weak learner. The way we fit this new week learner is we learn some kind of model, a weak model where the target variable is r1 hat, i.e all these gradients and the inputs are the same features we've always been using.![[Attachments/Pasted image 20240821105243.png]]
- **Step 4:**  How much of f2 to add to the current model.![[Attachments/Pasted image 20240821105814.png]]Finding loss using the target y and f1(x) + **γ**f2(x). Kind of recipe of how much do i want to add f2(x) because don't want to add too much i'm going to overshoot i don't want to add too little i'm going to undershoot. Officially called a line search which looks for the correct **γ** the correct amount of the new weak learner to add to the existing model so far which is going to minimize the sum across all loss functions for all n observations.
- **Step 5:** ![[Attachments/Pasted image 20240821110739.png]]
- ![[Attachments/Pasted image 20240821111053.png]]
- Its amazing: 
	- Can solve all types of problems, regression, classification, ranking
	- Any loss func, as long as its easily differentiable. NOTE: In GBTs, we just use the residual as the loss function, as easy as it can get.
	- Any weak learner works, diff models can be chosen for regression, classification, etc. Practically found that if your weak learners are decision trees whether or not this is a classification regression ranking this does really really really well.
- Drawbacks:
	- Not so intuitive
	- Computationally expensive
	- Easily overfit, bcs large DOF
