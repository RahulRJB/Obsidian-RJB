
# Partial Dependency Plot (pdp)


DATE:  29-12-24


Tags: [[Notes/Statistics|Statistics]]

# References:




# Content:

PDP is a valuable tool for understanding how individual features influence the predictions of a machine learning model. They're especially useful when you're working with complex models where the relationship between features and predictions isn't always obvious.

**The Challenge of Understanding Complex Models**

Many modern machine learning models, such as boosted trees, neural networks, and support vector machines, are often considered "black boxes." While they can achieve high predictive accuracy, it's difficult to understand how they arrive at their predictions. We often want to know:

- What is the relationship between a specific input feature and the model's output?
- Does increasing the value of a feature increase or decrease the model's prediction?
- Are there any non-linear relationships or interaction effects between the input feature and the prediction?

Partial dependence plots help us answer these questions by visualizing the marginal effect of a feature on the model's predictions.

**What is a Partial Dependence Plot?**

A partial dependence plot (PDP) shows the average predicted outcome of a machine learning model as a function of one or more input features. It marginalizes out the influence of all other features in the model so that we can examine the isolated effect of the variable we're interested in.

**Key Idea:**

The core idea is to isolate the effect of a feature of interest by:

1. **Varying the Feature of Interest:** Taking your feature of interest, and setting this feature to a specific value (or range of values).
2. **Marginalizing Out Other Features:** For each value of this feature, taking all the original samples and holding the feature of interest constant. Then, calculate all the predictions with this modified dataset. You can think of this as replacing the original feature values with the specific value of the feature that you're investigating. Then, take the average of all these predictions. This is the "partial" or "marginal" aspect of the calculation.
3. **Plotting the Average Prediction:** Plotting the average prediction against the corresponding feature value.

**How to Create a Partial Dependence Plot (Step-by-Step):**

1. **Select a Feature of Interest:** Choose the input feature you want to analyze.
2. **Define a Range of Values:** Determine the range of values you want to explore for the selected feature. This could be the actual range of the feature in your data or a subset of that range.
3. **Create Modified Data Sets:** For each chosen value in this range, create modified versions of your original data.
    - Take every observation from your original dataset.
    - For each observation, replace the original feature value with your current feature value under investigation.
4. **Generate Model Predictions:** Use your trained machine learning model to make predictions with all the modified datasets.
5. **Calculate Average Predictions:** For each of the values of the feature of interest, calculate the average of all the predictions generated from each observation.
6. **Plot the Results:** Create a plot with the range of feature values on the x-axis and the average predicted outcomes on the y-axis. This resulting plot is your PDP.

**Mathematical Formulation (Simplified):**

Let's say you have a machine learning model f(x) where x is a vector of input features x = (x₁, x₂, ..., xₙ), and you're interested in the partial dependence of the model on feature xᵢ. The partial dependence function is defined as:

```
PDᵢ(xᵢ) = Eₓ₋ᵢ [f(xᵢ, x₋ᵢ)]
```

Where:

- PDᵢ(xᵢ) is the partial dependence function of feature xᵢ
- Eₓ₋ᵢ is the average across the marginal distribution of the other features (where the other features are all features apart from xᵢ, denoted by x₋ᵢ).
- f(xᵢ, x₋ᵢ) is the prediction from your model with feature xᵢ and the remaining features, x₋ᵢ.

In practice, because we do not have access to the true underlying distribution, the average is calculated using all the observations from our training data, with their respective values for features other than our feature of interest.

**Interpretation of Partial Dependence Plots:**

- **Linear Relationship:** A straight line indicates a linear relationship between the feature and the average prediction.
- **Non-Linear Relationship:** A curved line suggests a non-linear relationship, where the effect of the feature changes at different values.
- **Increasing/Decreasing Effect:** An upward slope indicates that increasing the value of the feature increases the average predicted outcome. A downward slope means increasing the value of the feature decreases the outcome.
- **Plateau:** A flat line means the feature has little to no influence on the average predicted outcome.
- **Interactions:** In general PDPs do not take interactions into account (see "Limitations" below).

**Example:**

Let's say you have a model that predicts housing prices.

- **Feature of interest:** Square footage.
- A PDP might show a generally upward trend, indicating that, on average, larger houses have higher predicted prices.
- The plot may not be perfectly straight, suggesting non-linearity. For example, at some point, the predicted price might start to rise less for each additional square foot as the house reaches a certain size.
- Another PDP for number of bedrooms may initially increase before plateauing or even decreasing after a certain number of bedrooms.

**When to Use Partial Dependence Plots:**

- **Understanding Model Behavior:** When you want to gain insights into how individual features influence the predictions of your model.
- **Feature Interactions:** For certain features, it may be helpful to plot a 2D PDP in order to identify interaction effects. A 2D PDP is when the average prediction from the model is plotted against two variables of interest, creating a heatmap or contour plot.
- **Feature Engineering:** When you want to evaluate the effectiveness of certain features for building new variables.
- **Model Debugging:** When you want to diagnose why a model makes particular predictions.
- **Communicating Insights:** To present model results to a broader audience, as the plots are very easy to understand.

**Limitations:**

- **Marginal Effects:** PDPs only show marginal effects, not the full relationship between the feature and the prediction. The influence of the other features is marginalized out, which may not reflect how a feature truly interacts with others.
- **Interaction Effects:** A limitation of PDPs is that they do not take into account the interaction between different features. This makes PDPs less useful if interaction effects are important in the relationship between features and model prediction. This is why 2D PDPs can be useful to visualize possible interaction effects between two features, if known beforehand.
- **Computational Cost:** For complex models or large datasets, creating PDPs can be computationally intensive. This will depend on the model you are using and the number of predictions required.
- **Unrealistic Data Points:** As we are marginalizing over other features, it is possible to generate unrealistic datapoints with the other features. For example, you may not have realistic feature values like: a house with a small square footage but many bedrooms and bathrooms, and this type of data may not be represented in the data, or may not exist. This will result in predictions that are for unrealistic scenarios.
- **Misinterpretation:** The interpretation of PDPs can be tricky, especially for non-linear or complex relationships. The average is calculated for all the values of the other variables, even if the features of interest do not interact with them. This can also result in the partial effects becoming flattened, especially if feature interactions are important.