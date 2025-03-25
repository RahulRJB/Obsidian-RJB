
# Variance


DATE:  25-03-25


Tags:  [[Notes/Statistical ML|Statistical ML]] [[Notes/Overfitting|Overfitting]] [[Notes/Bias|Bias]]

# References:




# Content:

- Bias and Variance are something inherent to the ML algorithm. It majorly comes from the model itself and little from the data the model is trying to map.

"Variance" refers to the sensitivity of a model to fluctuations in the training data. Essentially, it measures how much the model's predictions change when trained on different subsets of the data. Here's a breakdown:

### **Understanding Variance**

- **Sensitivity to Data:**
    - High variance occurs when a model is overly sensitive to the specific details of the training data, including noise.
    - This leads to "overfitting," where the model performs very well on the training data but poorly on unseen data.  
    - Imagine a model that tries to fit every single data point perfectly, including random fluctuations. It will create a very complex and wiggly line that doesn't generalize well to new data.  

- **Variability in Predictions:**
    - High variance means that the model's predictions change significantly depending on which subset of the training data is used.


### **Sources of Variance in Machine Learning**

Variance can arise from several sources:

- **Complex Models:**
    - Models with high complexity, such as deep neural networks or high-degree polynomial regressions or Decision Trees (having high degree of freedom), tend to have high variance. These models have many parameters that can be adjusted to fit the training data very closely.  
- **Small Training Datasets:**
    - When the training dataset is small, the model is more likely to learn the noise in the data, leading to high variance. With less data, small changes in the training set can cause large changes in the learned model.
- **Noisy Data:**
    - If the training data contains a lot of noise (random errors or irrelevant information), the model may learn those noisy patterns, resulting in high variance.  
- **Too many features:**
    - When a model is trained on a dataset with a very large number of features, it can lead to the model learning very specific relationships that are only present in the training set, and not in other data.

**Why Variance Matters**

High variance can significantly impact the performance of machine learning models:  

- **Poor Generalization:**
    - Models with high variance tend to perform poorly on unseen data, as they have learned the noise in the training data rather than the underlying patterns.  
        
- **Unreliable Predictions:**
    - The predictions of high-variance models can be unstable and vary significantly depending on the specific training data used.

