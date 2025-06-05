
# Bradley-Terry Model


DATE:  05-06-25


Tags:  [[Notes/Maximum Likelihood|Maximum Likelihood]] [[Notes/Statistics|Statistics]] 

# References:



# Content:

**The key insight is that pairwise comparisons contain much richer information than simple aggregate statistics, and the Bradley-Terry model is an elegant way to extract that information.**

The Bradley-Terry model is a foundational statistical method for comparing pairs of items and ranking them based on observed outcomes. Think of it as a mathematical framework that helps us answer questions like "Which team is truly better?" or "What makes one product preferred over another?" when we have data from head-to-head comparisons.

## Understanding the Core Concept

Imagine you're trying to rank chess players based on their match results. Traditional win-loss records don't tell the whole story because not every player faces every other player, and some opponents are stronger than others. The Bradley-Terry model solves this by estimating each player's underlying "strength" or "ability" parameter, which represents their true skill level.

The model assumes that when two items (let's call them i and j) compete, the probability that item i wins follows a specific mathematical relationship. If we denote the strength parameters as π_i and π_j, then the probability that item i beats item j is:

P(i beats j) = π_i / (π_i + π_j)

This elegant formula captures an intuitive idea: the stronger an item is relative to its opponent, the more likely it is to win. The model ensures that these probabilities are always between 0 and 1, and that P(i beats j) + P(j beats i) = 1.

## The Mathematical Foundation

The Bradley-Terry model belongs to the family of log-linear models. When we take the logarithm of the odds ratio, we get:

log(P(i beats j) / P(j beats i)) = log(π_i) - log(π_j)

This linear relationship in the log-odds space makes the model mathematically tractable and connects it to logistic regression. The strength parameters π_i are typically estimated using maximum likelihood estimation, which finds the values that make the observed data most probable under the model.

## Why Bradley-Terry Evaluation Matters

This approach has profound applications across many fields. In sports analytics, it provides more nuanced rankings than simple win-loss records. In product evaluation, it helps identify consumer preferences from choice data. In machine learning, it's used to evaluate and compare different models or algorithms. The model's strength lies in its ability to handle incomplete comparison data and provide uncertainty estimates for rankings.


## What Makes Bradley-Terry Powerful

**Mathematical Consistency**: Notice how the Bradley-Terry predictions are more conservative than the raw win percentages. For example, Warriors beat Celtics 73.3% of the time historically, but the model predicts only 54.2%. This happens because the model considers that the Celtics might have been unlucky in that particular matchup, or that the sample size was small.

**Transitivity**: The model automatically ensures that if Team A is stronger than Team B, and Team B is stronger than Team C, then Team A will be predicted to beat Team C with appropriate probability. Simple win percentages can't guarantee this - you might have circular rankings where A beats B, B beats C, but C beats A.

**Strength of Schedule**: The model recognizes that beating strong opponents is more impressive than beating weak ones. A team with a 60% win rate against strong opponents might actually be better than a team with 70% against weak opponents.

## Applications Beyond Sports

The Bradley-Terry model has remarkable versatility:

**Product Rankings**: Companies like Netflix use similar models to rank movies based on user preferences. Instead of game outcomes, you have "Product A preferred over Product B" data.

**Search Engine Rankings**: Web search algorithms use pairwise comparison data to rank pages. Each "click" on result A over result B provides comparison information.

**Machine Learning Model Evaluation**: When comparing different ML models, Bradley-Terry can rank them based on pairwise performance comparisons across different datasets or metrics.

**A/B Testing**: Instead of just looking at conversion rates, you can use Bradley-Terry to rank different website designs based on user preference data.


