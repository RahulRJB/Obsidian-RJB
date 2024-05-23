# SHAP


DATE:  23-05-24


Tags:

# References: 
https://www.youtube.com/watch?v=VB9uV-x0gtg&t=31s



## Intro:
- SHapley Additive exPlanations
- Shapley values stems from Game Theory. Invented by Lloyd Shapley.
- Shapley is a way of providing a fair solution to the following question: 
	- If we have a coalition **C**, a group of cooperating members, that collaborates to produce a value **V**, called the coalition value. How much did each individual member contribute to that final value?
	- ![[Attachments/Pasted image 20240523171919.png]]
- Answering this can get tricky when there are interacting effects between members, when certain permutations can cause members to contribute more than the sum of their parts.


## Shapley Calculation:

- To find a fair answer we compute the Shapley value for each member of the coalition. 
- To compute the Shapley value for member 1 of our example coalition, we sample a coalition that contains member 1, and then looking at the coalition formed by removing that member. We then look at the respective values of these two coalitions, and compare the difference between the two. This difference is the marginal contribution of member 1 to the coalition consisting of members 2, 3, and 4 or how much did member 1 contribute to that specific group.
	- ![[Attachments/Pasted image 20240523172551.png]]
- Then we enumerate all such pairs of coalitions, i.e. all pairs of coalitions that only differ based on whether or not member 1 is included, and then look at all the marginal contributions  for each. The mean marginal contribution is the Shapley value of that member. 
	- ![[Attachments/Pasted image 20240523172618.png]]
- We repeat this same process for each member of the coalition, to get their shapley values.


## SHAP Calculation:


- Now, using this shapley values for model explananility is done in a very specific way. 
	- If we have a set a of inputs **x**, and a model **f(x)**, we can define a set of simplified local inputs **x’** (which usually means that we turn a feature vector into a discrete  binary vector, where features are either included or excluded) and an explanatory model **g(x’)**. 
		- ![[Attachments/Pasted image 20240523185315.png]]
	- We need to ensure that if x’≈x, then g(x’)≈f(x).
		- ![[Attachments/Pasted image 20240523190413.png]]
	- g(x’) must take the form:![[Attachments/Pasted image 20240523190615.png]] where φ_0 is the null output of the model, i.e. the average output of the model, and φ_i is the explained effect of feature i, i.e. how much that feature changes the output of the  model. This is called it’s attribution.

- If we have these two, we have an explanatory model that has additive feature attribution. The advantage of this form of explanation is that it is really easy to interpret; we can see the exact contribution and importance of each feature  just by looking at the φ values.



## Code:

- ![[Attachments/Pasted image 20240523195042.png]]
- ![[Attachments/Pasted image 20240523195150.png]] 
	- E[f(x)] is the average predicted output across all the datapoints provided.  
	- f(x) is the actual prediction for this sample.
	- SHAP values are all these values in between they tell us how each feature has contributed to the difference between the prediction and the average  prediction. For eg. shucked weights has increased the predicted output by 1.68.
	- Lastly all these numbers on the left are the actual feature values.

- ![[Attachments/Pasted image 20240523195856.png]]
- ![[Attachments/Pasted image 20240523195938.png]]
- ![[Attachments/Pasted image 20240523200018.png]]![[Attachments/Pasted image 20240523200105.png]]

- The single most useful SHAP plot, Beeswarm plot.
	- On the y-axis we have the values grouped by the different features and the color of the points are determined by the feature values. So higher values are redder. 
	- On the x-axis we have the SHAP values. Beeswarm can be used to highlight important relationships. We can see which features have large positive or large negative shap values in fact these features have been ordered in the same order as the mean shap plot.
	- We can also use this plot to start to understand the nature of these relationships. For Shell weights notice that as the feature values increase the shap values also increase. You may also notice that the relationship for shucked weights is the opposite.
	- ![[Attachments/Pasted image 20240523200131.png]]

- Dependence Plot can be used to try to understand what's going on. It is just a scatter plot of the SHAP values vs the feature values for a single feature and they are particularly useful if the feature has a non-linear relationship with the target variable.
![[Attachments/Pasted image 20240523200910.png]]![[Attachments/Pasted image 20240523201112.png]]
