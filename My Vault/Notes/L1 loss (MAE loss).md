
# L1 loss (MAE loss)


DATE:  11-09-24


Tags: [[Notes/Loss|Loss]]

# References:




# Content:

**L1 Loss**, also known as **Mean Absolute Error (MAE)** or **L1 Norm Loss**, is another commonly used loss function, particularly in regression problems. Unlike L2 Loss, which measures the squared differences between the predicted and actual values, L1 Loss measures the **absolute differences**.

### Mathematical Definition

For a set of *n* samples, the L1 Loss is defined as:

![[Attachments/Pasted image 20240911033906.png]]

### Characteristics of L1 Loss

1. **Less Sensitive to Outliers**: Unlike L2 Loss, L1 Loss does not square the differences between predicted and actual values, so it is less sensitive to outliers. This makes it a better choice when the dataset contains a lot of noise or extreme values.
    
2. **Sparsity-Inducing**: L1 Loss encourages sparsity in the model's coefficients, which is useful in feature selection. For example, in Lasso Regression (Least Absolute Shrinkage and Selection Operator), L1 Loss is used to enforce sparsity, meaning it helps in selecting only the most relevant features.
    
3. **Non-Differentiable at Zero**: The L1 Loss is not differentiable at zero (when the predicted value is exactly equal to the actual value), but it can still be optimized using sub-gradient methods or other techniques.
    
4. **Convex but Not Smooth**: L1 Loss is convex, which guarantees a global minimum, but it is not smooth like L2 Loss. This can make optimization slightly more challenging in some scenarios, but it's often worth it for the benefits it provides.
    

### Key Differences Between L1 Loss and L2 Loss

| Feature                     | L1 Loss (MAE)                              | L2 Loss (MSE)                            |
| --------------------------- | ------------------------------------------ | ---------------------------------------- |
| **Formula**                 | ( \frac{1}{n} \sum                         | y_i - \hat{y}_i                          |
| **Sensitivity to Outliers** | Less sensitive                             | More sensitive                           |
| **Penalization**            | Penalizes all errors equally               | Penalizes larger errors more heavily     |
| **Sparsity**                | Encourages sparsity in model weights       | Does not encourage sparsity              |
| **Optimization**            | Not differentiable at zero (sub-gradients) | Differentiable everywhere                |
| **Use Cases**               | Suitable for data with outliers/noise      | Suitable for normally distributed errors |

### When to Use L1 Loss vs. L2 Loss?

- **Use L1 Loss** when you want to reduce the impact of outliers or when you need a sparse model (few non-zero parameters).
- **Use L2 Loss** when you want to penalize larger errors more heavily and have normally distributed errors in your data.


