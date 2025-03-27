

# ROC-AUC


DATE:  10-11-24


Tags: [[Evaluation]]

# References:




# Content:

The ROC-AUC curve is a graphical representation and metric used to evaluate the performance of binary classification models. It helps to measure how well a model distinguishes between two classes.
### 1. ROC Curve (Receiver Operating Characteristic Curve)

- The ROC curve plots the **True Positive Rate (TPR)** (also called Sensitivity or Recall) against the **False Positive Rate (FPR)** (also called 1-Specificity) at various threshold levels.
    - **True Positive Rate (TPR)**: The ratio of correctly predicted positive cases to all actual positive cases. TPR = TP / (TP + FN) i.e   (Recall of pos. class)
    - **False Positive Rate (FPR)**: The ratio of incorrectly predicted positive cases to all actual negative cases. FPR = FP / (FP + TN) i.e  ( 1 - Recall of neg. class)
- The ROC curve shows how the TPR and FPR change as you adjust the decision threshold of the model. 
- At higher thresholds, the model would be much more restrictive to classifying any datapoint as positive. So at increasingly higher thresholds, the Recall or TPR starts to drop and FPR would be close to 0. That's the graph's starting point.
- As we start reducing the threshold level, the recall increases because more datapoints are marked as positive but FPR increases along with it as model starts to mark negative cases as positive as well. 
### 2. AUC (Area Under the Curve)

- **AUC** is the area under the ROC curve and is a single scalar value that summarizes the model's ability to distinguish between the positive and negative classes across all threshold values.
- **Interpretation**:
    - **AUC = 1**: Perfect model (100% sensitivity and specificity).
    - **AUC = 0.5**: No discrimination, equivalent to random guessing.
    - **AUC < 0.5**: Worse than random guessing (rare and indicates a flawed model or data).
    - Generally, **higher AUC values** indicate better model performance. For many applications, an AUC of 0.7-0.8 is considered acceptable, 0.8-0.9 is excellent, and >0.9 is outstanding.

### 3. Interpretation of the ROC-AUC Curve

- **Closer to the top-left corner**: A curve closer to the top-left corner represents better performance as it maximizes the TPR while minimizing the FPR.
- **Diagonal line (AUC = 0.5)**: Indicates no predictive power (the model performs no better than random chance).

### 4. Usefulness of ROC-AUC in Modeling

- **Threshold-independent evaluation**: ROC-AUC evaluates the model's performance across all possible thresholds, making it a robust measure of separability.
- **Comparing models**: AUC allows you to compare multiple models using a single number. A model with a higher AUC is generally better at distinguishing between the classes.
- Also used to compare different hyperparameter settings of the same model.
- **Class-imbalance robustness**: Unlike metrics like accuracy, AUC-ROC can be useful even if the classes are imbalanced, as it focuses on the model's sensitivity and specificity instead, both of them being ratios.
- **Trade-off visualization**: Shows the trade-off between sensitivity and specificity



