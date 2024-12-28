
# Cramer's V


DATE:  29-12-24


Tags: [[Notes/Statistics|Statistics]]

# References: 




# Content:

Cramer's V, also known as Cramer's coefficient or Cramer's phi. It's a measure of association between two categorical variables, often used when analyzing contingency tables. It's a powerful tool for understanding if there's a relationship between two categorical factors and the strength of that relationship.

**What is a Contingency Table?**

Before we dive into Cramer's V, it's important to understand what a contingency table is. A contingency table (also known as a cross-tabulation or crosstab) is a table that shows the frequency distribution of two or more categorical variables. It helps you visualize the relationships between these variables.

For example, if you wanted to study the relationship between a person's gender and their preferred mode of transportation, you might have a table like this:

|   |   |   |   |   |
|---|---|---|---|---|
||Bike|Car|Bus|Total|
|**Male**|30|70|50|150|
|**Female**|40|50|60|150|
|**Total**|70|120|110|300|

Here, "Gender" and "Mode of Transportation" are your categorical variables. The values in the table show the counts of people in each combination of the categories (e.g., 30 males prefer bikes).

**What is Cramer's V?**

Cramer's V is a statistic used to measure the strength of association (or correlation) between two categorical variables displayed in a contingency table. It is a normalized version of the Chi-squared statistic, making it more useful for interpreting the strength of the relationship when contingency tables of varying sizes are compared.

**Key Characteristics of Cramer's V:**

- **Range:** Cramer's V ranges from 0 to 1 (inclusive).
    - **0:** Indicates no association between the two categorical variables. The categories are independent of one another.
    - **1:** Indicates perfect association. The categories are perfectly correlated.
- **Symmetric:** It does not matter which variable is considered the "row" variable and which is the "column" variable.
- **Based on Chi-Squared:** Cramer's V is calculated using the chi-squared (χ²) statistic, which assesses the difference between observed and expected frequencies in the contingency table.
- **Handles Larger Tables:** Unlike some association measures, Cramer's V works well with contingency tables that have more than two rows or columns.
- **Interpretation:** It provides a standardized measure of association, making it easier to compare the strength of relationships across different tables.
    

**Formula for Cramer's V:**

The formula for calculating Cramer's V is:
```
V = √(χ² / (n * min(k-1, r-1)))
```



