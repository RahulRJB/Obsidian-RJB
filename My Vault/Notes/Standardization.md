
# Standardization


DATE:  28-03-25


Tags:  [[Notes/Statistical ML|Statistical ML]]

# References:




# Content:

## **Definition:**
- Standardization scales data to have a mean of 0 and a standard deviation of 1. This is also known as Z-score normalization.
- It transforms data to fit a standard normal distribution.

- **When to use:**
    - When your data follows a Gaussian distribution (or approximately so).
    - Algorithms that assume a Gaussian distribution, such as linear regression and logistic regression.
    - Algorithms where maintaining the relative relationships between data points is crucial.
    - When dealing with outliers, as standardization is less sensitive to them than normalization.
- **Key points:**
    - More robust to outliers.
    - Does not bound data to a specific range.



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



