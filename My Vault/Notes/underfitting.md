
# underfitting


DATE:  25-03-25


Tags: [[Notes/Statistical ML|Statistical ML]] [[Notes/Overfitting|Overfitting]] [[Notes/Bias|Bias]] 

# References:


# Content:

In machine learning, underfitting occurs when a model is too simple to capture the underlying patterns in the training data. This results in poor performance, not only on the training data itself, but also on any new, unseen data. Essentially, the model fails to learn the significant relationships between the input features and the target variable.  

### **Understanding Underfitting**

- **Oversimplification:**
    - An underfitted model is too basic, lacking the complexity needed to represent the data's inherent structure.  [[Bias]]
    - It fails to identify the dominant trends and relationships.
 
- **Poor Performance:**
    - The model exhibits high bias, meaning it consistently makes errors because of its overly simplistic assumptions.
    - It performs poorly on both the training and test datasets.


### **Sources of Underfitting**

Underfitting can stem from several factors:

- **Simple Model:**
    - Using a model that is too basic for the complexity of the data is a primary cause. For instance, attempting to fit a linear regression model to data with a non-linear relationship.

- **Insufficient Training:**
    - If a model is not trained for long enough, it may not have the opportunity to learn the underlying patterns.  

- **Lack of Features:**
    - When the input features do not provide enough relevant information to predict the target variable, the model will struggle to learn meaningful relationships.

- **Excessive Regularization:**
    - While regularization is used to prevent overfitting, applying too much regularization can constrain the model, leading to underfitting. The regularization can prevent the model from learning the complexity that is in the data.

- **Inadequate Data Preprocessing:**
    - If the data is not properly cleaned or transformed, the model may struggle to find the underlying patterns.


**Consequences of Underfitting**

- **High Bias:** The model consistently makes errors due to its simplified assumptions.  
- **Poor Accuracy:** The model's predictions are inaccurate, both on the training and test datasets.
- **Limited Generalization:** The model fails to generalize to new, unseen data.