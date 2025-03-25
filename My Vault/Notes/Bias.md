
# Bias


DATE:  25-03-25


Tags:  [[Notes/Statistical ML|Statistical ML]]  [[Notes/underfitting|underfitting]]  [[Variance]]

# References:




# Content:

- Bias and Variance are something inherent to the ML algorithm. It majorly comes from the model itself and little from the data the model is trying to map.

"Bias" refers to the systematic errors that a model makes due to incorrect assumptions in the learning algorithm. Essentially, it's the difference between the average prediction of our model and the correct value we are trying to predict. Here's a breakdown:  

### **Understanding Bias**

- **Simplified Models:**
    - High bias often occurs when a model is too simple to capture the underlying complexity of the data. This leads to "underfitting," where the model fails to learn the relevant patterns.  
    - Think of it as trying to fit a straight line to a set of data points that clearly follow a curve. The line will consistently miss the actual data points.
    - **Bias in model is one of the reasons of [[underfitting]]**.
- **Systematic Errors:**
    - Bias results in consistent errors in the model's predictions. The model consistently misses the mark in a predictable way.  


### **Sources of Bias in Machine Learning**

Bias can creep into machine learning models from various sources:  

- **Data Bias (Training Data Bias):**
    - **Sampling Bias:** If the training data doesn't accurately represent the real-world population, the model will learn biased patterns. For example, a facial recognition system trained primarily on images of one ethnicity may perform poorly on others.
    - **Historical Bias:** If the data reflects existing societal biases, the model will perpetuate those biases. For example, if historical hiring data shows a preference for male candidates, an AI hiring tool trained on that data will likely exhibit the same bias.  
    - **Measurement Bias:** Bias that comes from errors in how the data was measured or collected.  
    - **Reporting bias:** This occurs when the data available does not accurately reflect reality. For instance, news reports may over or under represent certain events.


- **Algorithmic Bias:**
    - This type of bias stems from the assumptions inherent in the machine learning algorithm itself. Some algorithms are better suited for certain types of data than others.  
    - Also, flaws in the way the algorithm is designed, or how features are weighted, can introduce bias.

- **Human Bias:**
    - The people who design, develop, and deploy machine learning systems can introduce their own biases, consciously or unconsciously.  
    - This can occur during data collection, labeling, and feature selection.
    - Cognitive biases, which are systematic errors in human thinking, can also contribute to bias in AI systems.

- **Specification Bias:**
    - Bias that occurs from the way that input and output variables are selected, or how the model is specified.



**Why Bias Matters**

Bias in machine learning can have serious consequences, leading to:

- Unfair or discriminatory outcomes.
- Reduced accuracy and reliability of models.  
- Erosion of trust in AI systems.

