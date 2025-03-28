
# Normalization


DATE:  28-03-25


Tags:  [[Notes/Statistical ML|Statistical ML]]

# References:




# Content:


- **Definition:**
    - Normalization typically scales data to a specific range, often between 0 and 1. The most common method is Min-Max scaling.
    - It's useful when you want to bound your data within a specific interval.
- **When to use:**
    - When you need values within a specific range.
    - When the data distribution is not Gaussian.
    - Algorithms like k-Nearest Neighbors (KNN) and neural networks, which are sensitive to feature scales.
    - Image processing, where pixel values often need to be within a specific range.
- **Key points:**
    - Sensitive to outliers. Outliers can significantly affect the normalized range.



### [[Normalization]] vs  [[Standardization]]:

Here's a general guideline:

- **Use Normalization:**
    - If you need to bound your data within a specific range.
    - If your data does not have a Gaussian distribution.
    - When using algorithms that require data to be within a specific range.
- **Use Standardization:**
    - If your data has a Gaussian distribution.
    - If your algorithm is sensitive to the variance of your data.
    - When outliers are present in your data.
- **General recommendations:**
    - Often, standardization is more widely used than normalization, especially with algorithms like Support Vector Machines (SVMs), linear regression, and logistic regression.
    - Neural networks can work well with either, although normalization is often preferred for image data.
    - If you are unsure of your data distribution, standardization is often a safer choice.
