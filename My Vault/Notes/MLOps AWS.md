
# MLOps AWS


DATE:  13-07-24


Tags:  [[Notes/MLOps AWS|MLOps AWS]] [[AWS]]


# References:  
https://youtube.com/watch?v=UnAN35gu3Rw




# Content:

### Problem without MLOps
- ![[Attachments/Pasted image 20240713154549.png]]
- We need to monitor the model in production. A ML model is out of date, as soon as you've trained it. The world is constantly changing and evolving, so the older your model gets, the worse it gets at making predictions. By monitoring the quality of your model, you will know when it's time to retrain or perhaps gather new data for it. This is due to **data drift**
- Problems without MLOps![[Attachments/Pasted image 20240713162008.png]]
	- Any changes to the machine learning model requires manual actions by the data scientist in the form of re-running cells in a Jupyter Notebook 
	- Code which the data scientist produces is stuck in these  Jupyter Notebooks, which are difficult to version and difficult to automate.
	- Data scientist might have forgotten to turn on auto-scaling for the SageMaker endpoint, so it cannot adjust capacity according to the number of requests coming in. 
	- There's no feedback loop. If the quality of the model deteriorates, you would only find out through complaints from disgruntled users.
- 
