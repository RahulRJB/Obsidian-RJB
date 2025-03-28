
# Precision-Recall curve


DATE:  28-03-25


Tags:  [[Notes/Statistical ML|Statistical ML]]

# References:




# Content:





## [[ROC-AUC]] vs Precision-Recall curve:

**ROC-AUC (Receiver Operating Characteristic - Area Under the Curve)**

- **When to use it:**
    
    - **Balanced datasets:** ROC-AUC performs well when the classes are relatively balanced. It provides a good overall measure of how well the model distinguishes between positive and negative classes.  
        
    - **When false positives and false negatives are similarly important:** If the cost of a false positive is similar to the cost of a false negative, ROC-AUC is a suitable metric.
    - **When you want to evaluate the model's ability to rank instances:** ROC-AUC focuses on the model's ability to rank positive instances higher than negative instances.
    - It is less sensitive to class imbalance.
- **What it measures:**
    
    - ROC curve plots the True Positive Rate (TPR) against the False Positive Rate (FPR) at various threshold settings.  
        
    - AUC measures the area under the ROC curve, providing a single value that summarizes the model's overall performance.  
        

**Precision-Recall Curve**

- **When to use it:**
    
    - **Imbalanced datasets:** Precision-Recall curves are particularly useful when dealing with imbalanced datasets, where one class significantly outnumbers the other. In such cases, ROC-AUC can give overly optimistic results.  
        
    - **When the positive class is the focus:** If you're primarily concerned with the model's performance on the positive class, Precision-Recall curves provide a more informative assessment.  
        
    - **When false positives are highly costly:** If the cost of a false positive is much higher than the cost of a false negative, Precision-Recall curves are more relevant. For example, in fraud detection, you want to minimize false positives (flagging legitimate transactions as fraudulent).  
        
- **What it measures:**
    
    - Precision-Recall curve plots precision (the proportion of true positives among predicted positives) against recall (the proportion of true positives identified out of all actual positives).
    - It focuses on the performance of the positive class.

**Key Differences and Considerations:**

- **Sensitivity to Imbalance:** ROC-AUC is less sensitive to class imbalance, while Precision-Recall is more sensitive.  
    
- **Focus:** ROC-AUC considers both positive and negative classes, while Precision-Recall focuses primarily on the positive class.
- **Interpretation:** ROC-AUC provides a general measure of how well the model distinguishes between classes, while Precision-Recall highlights the model's ability to correctly identify positive instances.


