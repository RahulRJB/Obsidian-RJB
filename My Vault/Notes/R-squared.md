
# R-squared


DATE:  29-12-24


Tags: [[Notes/Statistics|Statistics]]

# References:




# Content:

R-squared (also known as the coefficient of determination) is a statistical measure that represents the proportion of the variance in a dependent variable (the one you're trying to predict) that is predictable from the independent variable(s) (the ones you're using to make predictions) in a regression model.

Think of it this way: R-squared tells you how well your regression model "fits" your data. It gives you a sense of how much of the variation in the outcome you're trying to explain can be attributed to the variables you've included in your model.

**Key Characteristics of R-squared:**

- **Range:** R-squared values range from 0 to 1 (inclusive).
    
    - **0:** Means that the model explains none of the variability in the dependent variable. The model does not fit your data at all.
    - **1:** Means that the model explains all of the variability in the dependent variable. The model perfectly fits the data.
        
- **Interpretation:** R-squared is often expressed as a percentage, so an R-squared of 0.75 can be interpreted as: "75% of the variance in the dependent variable is explained by the independent variable(s) in the model."
    
- **Model Fit:** It reflects how well the regression model explains the observed data, with higher R-squared values generally indicating a better fit. However, a high R² does not necessarily mean that the model is good, just that it fits the data.
    
- **Not about Causation:** R-squared measures association, not causation. It does not imply that the independent variables cause changes in the dependent variable.
    
- **Multiple Regression:** R-squared can be used in both simple linear regression (one independent variable) and multiple regression (multiple independent variables).
    

**The Underlying Math (Simplified):**

To understand R-squared, it's helpful to know a few other terms:

1. **Total Sum of Squares (SST):**
    
    - This measures the total variability of the dependent variable around its mean. It shows how much the observed data values vary overall.
        
    - SST = Σ(yᵢ - ȳ)²
        
        - yᵢ represents each individual data point of the dependent variable
        - ȳ represents the mean of all the yᵢ data points
        - Σ represents the summation of the values
            
2. **Residual Sum of Squares (SSR or SSE):**
    
    - This measures the variability of the dependent variable that is not explained by the model. It represents the total error of the model.
        
    - SSR = Σ(yᵢ - ŷᵢ)²
        
        - yᵢ is the observed data point
        - ŷᵢ is the predicted value of the dependent variable based on the regression model
            
3. **Regression Sum of Squares (SSReg):**
    
    - This measures the variability of the dependent variable that is explained by the model.
        
    - SSReg = SST - SSR
    - SSReg = Σ(ŷᵢ - ȳ)²
        

Then, R-squared is calculated as:
```
R² = SSReg / SST  = 1 - (SSR / SST)
```

**In simpler terms:**

R-squared is the ratio of the variation explained by the model to the total variation in the dependent variable. Or, put another way, it's "1 minus the ratio of variation not explained by the model to the total variation."

**Examples:**

- **Example 1: Housing Prices:**
    
    - Let's say you build a regression model to predict house prices using the size of the house (in square footage) as the predictor variable.
        
    - If your model has an R-squared of 0.80 (80%), it means that 80% of the variation in house prices can be explained by the size of the house. The remaining 20% is unexplained by your model.
        
- **Example 2: Exam Scores:**
    
    - Suppose you build a regression model to predict exam scores using study hours.
        
    - If your R-squared is 0.30 (30%), it means that study hours explain only 30% of the variance in exam scores. Other factors (aptitude, sleep, etc.) contribute to the remaining 70%.
        

**Limitations and Cautions:**

- **Doesn't Tell the Whole Story:** A high R-squared value doesn't necessarily mean the model is perfect or good for all purposes. It only speaks to the fit of the model to this data. A high R² can be misleading if the dataset has issues with bias or if the model is overfitted.
    
- **R² is always relative to the dataset.** A model can have a high R² for one dataset and a low R² for another.
    
- **Can be Misleading with Added Variables:** R-squared will always increase (or stay the same) when you add more independent variables to the model, even if those variables aren't truly meaningful or predictive. This is because the model will find some way to fit your variables into the model. This is where the concept of **Adjusted R-squared** comes into play (explained below).
    
- **Not for Non-Linear Models:** R-squared is designed primarily for linear regression models. It may not be as suitable for other model types (e.g., non-linear models).
    
- **Focus on Context:** The "goodness" of an R-squared depends on the context of the analysis. An R² of 0.4 might be considered good in some fields of study, but bad in other fields.
    

**Adjusted R-squared:**

Adjusted R-squared is a modified version of R-squared that penalizes the inclusion of unnecessary independent variables. It takes into account the number of predictors and the number of observations. The adjusted R-squared will decrease if an unnecessary predictor is added to the model, providing a better estimate of the model fit than the regular R².



