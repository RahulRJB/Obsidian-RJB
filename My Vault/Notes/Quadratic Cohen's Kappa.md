
# Quadratic Cohen's Kappa


DATE:  13-12-24


Tags: [[Notes/Evaluation|Evaluation]] [[Notes/Cohen's Kappa|Cohen's Kappa]]

# References:




# Content:

This is an extension of the standard Cohen's Kappa that provides a more nuanced measure of agreement for ordinal categorical data.

**Standard vs. Quadratic Cohen's Kappa** While standard Cohen's Kappa treats all disagreements as equally important, Quadratic Cohen's Kappa (sometimes called Weighted Kappa) recognizes that some disagreements are more severe than others, especially in ordinal categories.

**Key Characteristics**

- Designed for ordinal categorical data
- Assigns different weights to disagreements based on their magnitude
- Uses a quadratic weighting scheme that penalizes larger disagreements more heavily

**Mathematical Approach** The formula is similar to standard Cohen's Kappa, but introduces a weighting matrix:

κw = 1 - [Σ(wij * Oij) / Σ(wij * Eij)]

Where:

- wij is a weight matrix
- Oij is the observed agreement
- Eij is the expected agreement by chance

**Weighting Mechanism**

- Adjacent categories have smaller weights
- Distant categories have larger weights
- Typically uses a quadratic weight matrix

**Practical Example** Consider pain level assessments:

- A disagreement between 'mild' and 'moderate' is less serious
- A disagreement between 'mild' and 'severe' is much more significant

**When to Use**

- Medical assessments with ordinal scales
- Psychological rating scales
- Customer satisfaction surveys
- Any scenario with ordered categories

**Key Differences from Standard Cohen's Kappa**

1. Recognizes varying degrees of disagreement
2. Provides more nuanced agreement measurement
3. Particularly useful for ordinal data
4. Penalizes more distant category misclassifications

**Limitations**

- More complex to calculate
- Requires careful selection of weighting scheme
- Can be sensitive to the specific weighting approach



