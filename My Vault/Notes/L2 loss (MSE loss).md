
# L2 loss (MSE loss)


DATE:  11-09-24


Tags: [[Loss]]

# References:




# Content:

L2 Loss, also known as Mean Squared Error (MSE) or L2 Norm Loss, is a common loss function used in regression problems. It measures the average squared difference between the predicted values and the actual target values. The goal of using L2 Loss is to minimize this difference, which means reducing the error between the model's predictions and the actual data.

### Mathematical Definition

For a set of *n* samples, the L2 Loss can be defined as:
![[Attachments/Pasted image 20240911033739.png]]

### Characteristics of L2 Loss

1. **Penalizes Larger Errors More**: L2 Loss squares the difference between the predicted and actual values, which means it penalizes larger errors more heavily. This makes the model sensitive to outliers in the data.
    
2. **Smooth and Differentiable**: The L2 Loss is smooth and differentiable, making it suitable for gradient-based optimization techniques, such as gradient descent.
    
3. **Convexity**: The L2 Loss is convex, which means there is a single global minimum. This property makes it easier for optimization algorithms to find the optimal solution.
    

### Use Cases

L2 Loss is widely used in linear regression, neural networks, and other machine learning models where the target variable is continuous. It is particularly useful when the model needs to focus on minimizing large errors, as it aggressively penalizes them.



