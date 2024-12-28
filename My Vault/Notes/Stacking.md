
# Stacking


DATE:  29-12-24


Tags: [[Ensemble]]

# References:




# Content:

Stacking, also known as stacked generalization, takes a hierarchical approach to combining predictions. It involves training a new model, called the "meta-model" or "blender," to learn how to best combine the predictions of the base-level models.

**How Stacking Works (Step-by-Step):**

1. **Base Model Training:**
    
    - You train several different base-level models on your training data. These models can be different algorithms (e.g., a decision tree, a support vector machine, a neural network) or variations of the same algorithm with different parameters.
        
    - Crucially, you need to use **K-Fold cross-validation** during the training of these base models. This is important to avoid overfitting to the training data. The idea is to get out-of-fold predictions, which are the predictions made on folds of data where a particular base model was not trained on. These out-of-fold predictions act as input features for our meta-model.
        
    - Let's say you have a dataset, split into 5 folds.
        
    - You train your model on 4 folds, then make predictions on the remaining 5th fold. You repeat this with all 5 folds, each time holding out a different fold to predict on. This means that for every row in your original dataset, you'll have out-of-fold predictions for each base-level model.
        
2. **Creation of Meta-Model Training Data:**
    
    - The out-of-fold predictions from the base models become the new input features for your meta-model.
        
    - So, the rows in this new training dataset are the original rows of data from your original dataset, but the columns are the out-of-fold predictions of your base models. The target values are the same as the original dataset (i.e. your y).
        
3. **Meta-Model Training:**
    
    - You train the meta-model on this new training dataset (comprised of base-model predictions). The meta-model is typically a simpler model, such as a linear regression or logistic regression, but it could also be another more complex model. The purpose of the meta-model is not to be a great model, but to be able to learn how much to trust the predictions of the base-level models.
        
    - The meta-model learns to weigh and combine the predictions from the base-level models in a way that optimizes performance on the validation data.
        
4. **Making Predictions on New Data:**
    
    - For new, unseen data, you first run each base-level model to generate predictions.
        
    - Then, you feed these predictions into the trained meta-model.
        
    - The meta-model outputs the final, combined prediction.
        

**Key Characteristics of Stacking:**

- **Hierarchical Structure:** Two layers of models – base models and a meta-model.
    
- **Meta-Model Training:** The meta-model learns to weigh base model predictions.
    
- **Out-of-Fold Predictions:** Crucial to prevent overfitting during base model prediction aggregation.
    
- **Computational Complexity:** Can be more computationally expensive due to cross-validation and meta-model training.

**Key Differences Summarized:**

|   |   |   |
|---|---|---|
|Feature|Stacking|Blending|
|**Structure**|Hierarchical (base models + meta-model)|Direct combination of base model predictions|
|**Meta-Model**|A model is trained to combine predictions.|No new model is trained|
|**Training Data**|Uses out-of-fold predictions during cross-validation.|Uses a separate validation dataset|
|**Computational Cost**|More computationally expensive due to cross-validation.|Less computationally intensive|
|**Overfitting**|More resistant to overfitting due to cross-validation|Can be more prone to overfitting if the validation set is not sufficient.|
|**Flexibility**|Can be more flexible due to meta-model|Less flexible|
|**Complexity**|More complex to implement|Simpler and easier to implement.|

**When to Use Which:**

- **Stacking:** Use when you want maximum performance and have sufficient computational resources. It's generally considered more robust to overfitting and might lead to slightly better results than blending.
    
- **Blending:** Use when you want a simpler and faster ensemble method. It can provide good performance, especially when computational resources are limited.



