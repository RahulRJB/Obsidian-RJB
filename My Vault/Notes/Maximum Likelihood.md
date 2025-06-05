
# Maximum Likelihood  (log likelihood) (likelihood)


DATE:  16-01-25


Tags: [[Statistics]]

# References:  

https://www.youtube.com/watch?v=VOIhswqFWVc

# Content:

Demystifying maximum likelihood by building up the logic through a concrete example: predicting students' acceptance into their top-choice medical school.

**Problem Setup: Binary Classification with Logistic Regression**

- ![[Attachments/Pasted image 20250116183137.png]]
- **Goal:** To predict whether a student gets into their top-choice medical school (binary outcome: 1 for acceptance, 0 for rejection).
- **Data:
	- **x**: A data matrix with n students (rows) x p predictors (columns) such as GPA, MCAT scores, extracurriculars, etc.
	- **y**: A vector (n x 1) representing the outcome for each student (1 or 0).
- **Model:** Logistic Regression is used to model the probability of acceptance (p).


**The Logit Transformation and Linear Combination**
- ![[Attachments/Pasted image 20250116183353.png]]
- The log-odds (or logit) of the probability p is modeled as a linear combination of the predictors:
	- `logit(p) = ln(p/(1-p)) = β₀ + β₁x₁ + β₂x₂ + ... + βₚxₚ`
- This can be represented more compactly as: 
	- `logit(p) = β ⋅ x` 
	- where β is the vector of parameters and x is the vector of predictors for a given student. An intercept term (β₀) is included in the β vector, which requires adding a '1' as the first element of the x vector.


**Sigmoid Function and Probability Modeling**

- Solving for p, we get the sigmoid function:
	- `p = e^(β⋅x) / (1 + e^(β⋅x))`
- This equation means the probability of a student getting into their top-choice school is modeled as a function of β and x.
- Essentially, we are trying to capture the relationship between student data (x) and their probability of getting accepted, mediated by our model parameters (β).


**Individual Probability and Combining Outcomes**

- ![[Attachments/Pasted image 20250116183835.png]]
- The probability of a specific student `i` being accepted (yᵢ=1) or rejected (yᵢ=0) given their data (xᵢ) and the parameter vector (β) is expressed in the following form:
	- `P(yᵢ | xᵢ, β) = e^(yᵢ(β⋅xᵢ)) / (1 + e^(β⋅xᵢ))`
- The cleverness here is that the expression can be used to calculate both the probability of acceptance and rejection.
	- When yᵢ = 1 (acceptance), this simplifies to the probability of acceptance.
	- When yᵢ = 0 (rejection), this simplifies to the probability of rejection (1 - P(acceptance)).
- The importance here is we have a singular equation that captures both possible outcomes for a given student.


**Maximum Likelihood - The Core Concept**
- ![[Attachments/Pasted image 20250116184045.png]]

- **Goal:** Find the model parameters (β) that maximize the likelihood of observing the actual outcomes in our data.
- **Independence Assumption:** The probability of each student's outcome is assumed to be independent of others
- **Likelihood Function:** This is the probability of observing all outcomes for all students, given by the _product_ of individual probabilities:
	- L(y | x, β) = P(y₁ | x₁, β)  *  P(y₂ | x₂, β)  * ... *  P(yₙ | xₙ, β)
- The likelihood function can be thought of as the "probability of seeing all of the real world outcomes that I actually see in my data"
- We want the individual probabilities within the likelihood function to be as high as possible because that means the model is accurately reflecting the true outcomes: high probability to those who were actually accepted, low probability to those who were rejected.
- **Maximizing the Likelihood**: A good model should assign high probabilities to outcomes that were actually observed.
- The goal is to find the set of parameters that make the observed data most likely according to our model.


**Log-Likelihood and Optimization**
- ![[Attachments/Pasted image 20250116184451.png]]
- **Log Transformation:** The product of individual probabilities in the likelihood function results in small numbers that can cause computation(precision) problems. Therefore, a log transformation is applied to change the product to a sum:
	- log(L(y | x, β)) = Σᵢ log(P(yᵢ | xᵢ, β))
- This does not affect the location of the maximum, due to the monotonicity of logarithms.
- Using the previously derived individual probability, the log-likelihood function can be written as a sum of terms in the following form: 
- log(L(y | x, β)) = Σᵢ log(P(yᵢ | xᵢ, β))  ==>>   Σᵢ (yᵢ(β⋅xᵢ) - log(1 + e^(β⋅xᵢ)))

- **Derivative**:
- The derivative of the log-likelihood function is taken with respect to β to find the maximum.
- The resulting form is:   `Xᵀ(y - σ(Xβ))`
- σ(Xβ) represents the vector of predicted probabilities for each student.
- This derivative form shows that we want to minimize the difference between the actual outcomes y and the model's predicted probabilities σ(Xβ).
- **Maximum Likelihood Estimator (MLE):** The parameter vector β that maximizes the likelihood (or log-likelihood) is called the MLE.
- **No Closed-Form Solution:** Unfortunately, there is no simple closed-form solution to find the ideal β. Because β is contained within the σ function, we cannot just "solve" for β directly.


**Solving via Optimization**

- Although there is no closed form solution, there are numerical methods for finding the optimal β such as:
	- Gradient Ascent (or gradient descent on negative log-likelihood)
	- EM algorithm


**Intuitive Understanding of the Derivative**

- The core of the derivative analysis is the term y - σ(Xβ).
- When yᵢ = 1 (student accepted), we want σ(Xβ) to be large (high probability of acceptance).
- When yᵢ = 0 (student rejected), we want σ(Xβ) to be small (low probability of acceptance).
- Intuitively, we are trying to make the predictions line up with the reality of each students outcome.

**Key Quotes:**

- "We're saying that the probability of observing the actual y i given some students x i and given β is this... we are going to model the probability that you get into your top choice medical school by looking at all these data points about you as well as beta..."
- "The likelihood function is basically the sentence this is the probability of seeing all of the real world outcomes that i actually see in my data and if my model is good i would expect the probabilities output by my model to all the time closely match the actual outcome whether that's assigning a high probability to accept and assigning a low probability to rejects"
- "The goal of this problem... is to get a good parameter vector beta and what do we mean by good good in the sense that if we pick that beta the probabilities that are output by the model are going to closely match the truth."
- **"...The ideal beta vector the ideal parameter vector if we could find it would be called the maximum likelihood estimator or the mle estimate of beta."**
- "...The main ideas that I wanted to get across is that the maximum likelihood problem really stems from asking the question what is the probability of seeing the outcomes that I actually see in the real world"
