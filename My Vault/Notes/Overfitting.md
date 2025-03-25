
# Overfitting


DATE:  25-03-25


Tags:  [[Statistical ML]] [[Notes/Variance|Variance]] [[Notes/underfitting|underfitting]]

# References:




# Content:


Overfitting is a common problem in machine learning where a model learns the training data too well, capturing white noise and random fluctuations along with the underlying pattern. This leads to excellent performance on the training data but poor generalization to unseen data. 

### **Understanding Overfitting**

- **Memorization, Not Generalization:**
    - An overfitted model essentially "memorizes" the training data, including its idiosyncrasies.  
    - Instead of learning generalizable patterns, it learns specific details that may not be relevant to other datasets.

- **Poor Performance on New Data:**
    - The key consequence of overfitting is that the model fails to generalize to unseen data.  
    - When presented with new examples, it produces inaccurate predictions because it's too tailored to the training set.  

### **Sources of Overfitting**

Overfitting can arise from several sources:

- **Excessive Model Complexity:**
    - Using a model with too many parameters (e.g., a deep neural network with too many layers or a high-degree polynomial regression) allows it to fit even the noise in the data.
- **Insufficient Training Data:**
    - When the training dataset is small, the model has less data to learn from and is more likely to memorize the available examples, including their noise.  
- **Noisy Data:**
    - If the training data contains a significant amount of noise or errors, the model may learn those irrelevant patterns, leading to overfitting.  
- **Training for Too Long:**
    - Training a model for an excessive number of epochs (iterations) can cause it to gradually overfit the training data.  
- **Too many features:**
    - When there are too many features compared to the number of data samples, the model has an easier time finding relationships that do not generalize.

### **Consequences of Overfitting**

- **Reduced Generalization Ability:** The model performs poorly on unseen data.  
- **Unreliable Predictions:** The model's predictions become unreliable and inconsistent.  


### **Methods to Mitigate Overfitting**

- **Increase Training Data:** Providing more training data helps the model learn generalizable patterns.  

- **Simplify the Model:** Using a simpler model with fewer parameters reduces its capacity to memorize noise.

- **Regularization:** Techniques like L1 and L2 regularization penalize complex models, encouraging them to learn simpler patterns.
	- L1 regularization (Lasso): Adds the absolute value of the coefficients to the loss function, which can lead to feature selection (some coefficients become zero).  
    - L2 regularization (Ridge): Adds the squared value of the coefficients to the loss function, which shrinks the coefficients towards zero but doesn't eliminate them.  
    - Elastic Net: A combination of L1 and L2 regularization.

- **Cross-Validation:** This technique helps assess the model's performance on unseen data and detect overfitting.  

- **Early Stopping:** Monitoring the model's performance on a validation set and stopping training before it starts to overfit.

- **Feature Selection/Reduction:** Reducing the number of irrelevant or redundant features can improve generalization.

- **Dropout (for Neural Networks):** Randomly dropping out neurons during training prevents the network from becoming too reliant on specific features.

- **Data Augmentation:** Increasing the size of the training dataset by creating modified versions of existing data.  
	- **Techniques:**
	    - Rotating, scaling, and cropping images.  
	    - Adding noise to audio signals.
	    - Generating synthetic data.  
	
	- **Benefits:**
	    - Increases the diversity of the training data.
	    - Reduces overfitting.

- **Ensemble methods:**
	- **Concept:** Combining the predictions of multiple models to improve performance.
	- **Techniques:**
	    - Bagging: Training multiple instances of the same model on different subsets of the training data (e.g., Random Forest).
	    - Boosting: Training models sequentially, where each model tries to correct the errors of the previous models (e.g., Gradient Boosting).
	- **Benefits:**
	    - Reduces variance and improves generalization.

