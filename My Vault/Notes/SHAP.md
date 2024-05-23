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

## Calculation:

- To find a fair answer we compute the Shapley value for each member of the coalition. 
- To compute the Shapley value for member 1 of our example coalition, we sample a coalition that contains member 1, and then looking at the coalition formed by removing that member. We then look at the respective values of these two coalitions, and compare the difference between the two. This difference is the marginal contribution of member 1 to the coalition consisting of members 2, 3, and 4 or how much did member 1 contribute to that specific group.
	- ![[Attachments/Pasted image 20240523172551.png]]
- Then we enumerate all such pairs of coalitions, i.e. all pairs of coalitions that only differ based on whether or not member 1 is included, and then look at all the marginal contributions  for each. The mean marginal contribution is the Shapley value of that member. 
	- ![[Attachments/Pasted image 20240523172618.png]]
- We repeat this same process for each member of the coalition, to get a fair solution to our original question. 


 
They do this in a very specific way, one  that we can get a clue to by looking at  

the name of their algorithm; Shapley Additive  Explanations. We know what Shapley values are,  

we know what explanations are, but  what do they mean by additive?  

Lundberg and Lee define an additive feature  attribution as follows: if we have a set a of  

inputs x, and a model f(x), we can define a set  of simplified local inputs x’ (which usually means  

that we turn a feature vector into a discrete  binary vector, where features are either included  

or excluded) and we can also define an explanatory  model g. What we need to ensure is that  

One: if x’ is roughly equal to x then  g(x’) should be roughly equal to f(x),  

and two: g must take this form, where  phi_0 is the null output of the model,  

that is, the average output of the model, and  phi_i is the explained effect of feature_i;  

how much that feature changes the output of the  model. This is called it’s attribution.  

If we have these two, we have an explanatory  model that has additive feature attribution.  

The advantage of this form of explanation is  really easy to interpret; we can see the exact  

contribution and importance of each feature  just by looking at the phi values.  

Now Lundberg and Lee go on to describe  a set of three desirable properties of  

Local Accuracy, Missingness, and Consistency

such an additive feature method; local  accuracy, missingness, and consistency.  

We’ve actually already touched upon local  accuracy; it simply says if the input and the  

simplified input are roughly the same, then  the actual model and the explanatory model  

should produce roughly the same output.  Missingness states that if a feature is  

excluded from the model, it’s attribution  must be zero; that is, the only thing that can  

affect the output of the explanation model is the  inclusion of features, not the exclusion. Finally,  

we have consistency (and this one’s a little hard  to represent mathematically), but it states that  

if the original model changes so that the a  particular feature’s contribution changes,  

the attribution in the explanatory model  cannot change in the opposite direction;  

so for example, if we have a new model where a  specific feature has a more positive contribution  

than in the original; the attribution in our  new explanatory model cannot decrease.  

Now while a bunch of different explanation methods  satisfy some of these properties, Lundberg and Lee  

argue that only SHAP satisfies all three;  if the feature attributions in our additive  

explanatory model are specifically chosen  to be the shapley values of those features,  

then all three properties are upheld. The problem  with this, however, is that computing Shapley  

values means you have to sample the coalition  values for each possible feature permutation,  

which in a model explainability setting means we  have to evaluate our model that number of times.  

For a model that operates over 4 features, it’s  easy enough, it’s just 64 coalitions to sample  

to get all the Shapley values. For 32 features,  that’s over 17 billion samples, which is  

entirely untenable. To get around this,  Lundberg and Lee devise the Shapley Kernel,  

Shapley Kernel

a means of approximating shapley  values through much fewer samples.  

So what we do is we pass samples through  the model, of various feature permutations  

of the particular datapoint that  we’re trying to explain. Of course,  

most ML models won’t just let you omit a feature,  so what we do is define a background dataset B,  

one that contains a set of representative  datapoints that the model was trained over.  

We then fill in our omitted feature or features  with values from the background dataset,  

while holding the features that are included in  the permutation fixed to their original values.  

We then take the average of the model output  over all of these new synthetic datapoints as  

our model output for that feature permutation,  which we’ll call that y bar.  

So once we have a number of samples computed in  this way, we can formulate this as a weighted  

linear regression, with each feature assigned  a coefficient. With a very specific choice of  

weighting for each sample, based on a combination  of the total number of features in the model,  

the number of coalitions with the same  number of features as this particular sample,  

and the number of features included and excluded  in this permutation, we ensure that the solution  

to this weighted linear regression is such  that the returned coefficients are equivalent  

to the Shapley values. This weighting  scheme is the basis of the Shapley Kernel,  

and the weighted linear regression  process as a whole is Kernel SHAP.  

Now, there are a lot of other forms of SHAP that  are presented in the paper, ones that make use of  

model-specific assumptions and optimizations to  speed up the algorithm and the sampling process,  

but Kernel SHAP is the one among them that is  universal and can be applied to any type of  

machine learning model. This general applicability  is why we chose Kernel SHAP as the first form of  

SHAP to implement for TrustyAI; we want to be  able to cover every possible use-case first,  

and add specific optimizations later.  At the moment of recording this video,  

the 18th of March 2021, I’d estimate I’m about  85% done with our implementation, and it should  

be ready in a week or so. Since our  version isn’t quite finished yet,


