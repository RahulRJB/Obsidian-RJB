
# MLOps AWS


DATE:  13-07-24


Tags:  [[Notes/MLOps AWS|MLOps AWS]] [[AWS]]


# References:  
https://youtube.com/watch?v=UnAN35gu3Rw




# Content:

### Problem without MLOps
- ![[Attachments/Pasted image 20240713154549.png]]
- We need to monitor the model in production. A ML model is out of date, as soon as you've trained it. The world is constantly changing and evolving, so the older your model gets, the worse it gets at making predictions. By monitoring the quality of your model, you will know when it's time to retrain or perhaps gather new data for it. This is due to **data drift**
- Problems without MLOps:![[Attachments/Pasted image 20240713162008.png]]
	- Any changes to the machine learning model requires manual actions by the data scientist in the form of re-running cells in a Jupyter Notebook 
	- Code which the data scientist produces is stuck in these Jupyter Notebooks, which are difficult to version and difficult to automate.
	- Data scientist might have forgotten to turn on auto-scaling for the SageMaker endpoint, so it cannot adjust capacity according to the number of requests coming in. 
	- There's no feedback loop. If the quality of the model deteriorates, you would only find out through complaints from disgruntled users.

### MLOps with AWS:
- ![[Attachments/Pasted image 20240714201110.png]]

- ![[Attachments/Pasted image 20240714203551.png]]Reproductibility: One of the challenge, mentioned previously is that the code is stuck in Jupyter notebooks and can be difficult to version and automate. 
	- Add more versioning to this architecture. You can use [[CodeCommit]] or any other Git-based repository to store code. 
	- Use Amazon Elastic Container Registry or ECR to store Docker containers, thereby versioning the environments which were used to train the machine learning models. 
	- By versioning the code, the environments and the model artifacts, you improve you ability to reproduce models and collaborate with others.
- ![[Attachments/Pasted image 20240714203603.png]]Automation: Another challenge is that the data scientists are manually re-training models instead of focussing on developing new models. To solve this, we want to set up automatic re-training pipelines. We can use **SageMaker Pipelines**, but you could also use **Step Functions** or **Airflow** to build these repeatable workflows. Use version code and environments to perform data pre-processing, model training, model verification, and eventually save the new model artifacts to S3. It can use various services to complete these steps: SageMaker processing or training jobs, EMR, or Lambda. 
- To automate this pipeline, we need a trigger. One option is to use **EventBridge** to trigger the pipeline based on a schedule. Another option is to have someone manually trigger the pipeline.
- ![[Attachments/Pasted image 20240714203759.png]]Model registry: While S3 provides some versioning and object locking functionality, which is useful for storing different models, a model registry helps to manage these models and their versions. SageMaker model registry allows you to store metadata alongside your models, including the values of hyperparameters and evaluation metrics, or even the bias and explainability reports. This enables you to quickly view and compare different versions of a model and to approve or reject a model version for production. Actual artifacts are still stored in S3, but model registry sits on top of that as an additional layer.
- ![[Attachments/Pasted image 20240714204033.png]]Deployment stage: ML models are deployed on real-time **SageMaker endpoints** connected to Lambda and API Gateway to communicate with an application. The main difference now is that I have autoscaling set up for my SageMaker endpoints. So if there's an unexpected spike in users, the endpoints can scale up to handle the requests and scale back down when the usage falls. One nice feature of SageMaker endpoints is that you can replace the ML model without endpoint downtime.
- ![[Attachments/Pasted image 20240714204223.png]]We have automated re-training pipeline creating new models, and a model registry where I can approve models. We can make a Lambda function, which triggers when a new model is approved to fetch that model, and then update the SageMaker endpoint with it.
- ![[Attachments/Pasted image 20240714212034.png]]**Canary deployment:** Gradual deployment, Small portion of the user requests will be diverted to the new model, and any errors or issues will trigger a Cloud-Watch alarm to inform us. Over time, the number of requests sent to the new model will increase until the new model gets 100% of the traffic.
- ![[Attachments/Pasted image 20240714212320.png]]