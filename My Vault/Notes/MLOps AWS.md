
# MLOps AWS


DATE:  13-07-24


Tags:  [[Notes/MLOps AWS|MLOps AWS]] [[AWS]]


# References:  
https://youtube.com/watch?v=UnAN35gu3Rw
https://www.youtube.com/watch?v=T9llSCYJXxc&t=1s



# Content:

### Problem without MLOps
- ![[Attachments/Pasted image 20240713154549.png]]
- We need to monitor the model in production. A ML model is out of date, as soon as you've trained it. The world is constantly changing and evolving, so the older your model gets, the worse it gets at making predictions. By monitoring the quality of your model, you will know when it's time to retrain or perhaps gather new data for it. This is due to **data drift**
- Problems without MLOps:![[Attachments/Pasted image 20240713162008.png]]
	- Any changes to the machine learning model requires manual actions by the data scientist in the form of re-running cells in a Jupyter Notebook 
	- Code which the data scientist produces is stuck in these Jupyter Notebooks, which are difficult to version and difficult to automate.
	- Data scientist might have forgotten to turn on auto-scaling for the SageMaker endpoint, so it cannot adjust capacity according to the number of requests coming in. 
	- There's no feedback loop. If the quality of the model deteriorates, you would only find out through complaints from disgruntled users.

### MLOps tools with AWS :
- ![[Attachments/Pasted image 20240714201110.png]]
#### SMALL SETUP

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


#### MEDIUM SETUP:
- Same as small setup for the development. But uses development account:![[Attachments/Pasted image 20240714215745.png]]
- Deployment in a separate AWS prod account.  A multi-account strategy is highly recommended, because this allows you to separate different business units, easily define separate restrictions for important production workloads, and have a fine-grained view of the costs incurred by each component of your architecture. The different accounts are best managed through AWS Organizations.![[Attachments/Pasted image 20240714220518.png]] 
- Data scientists should not have access to the production account. This reduces the chance of mistakes being made on that account, which directly affects your users. In fact, a multi-account strategy for machine learning usually has a separate staging account alongside the production account. Any new models are first deployed to the staging account, tested and only then deployed on the production account.![[Attachments/Pasted image 20240714220758.png]]
- If the data scientist cannot access these accounts, the deployment must happen automatically. All of the services deployed into the staging and production accounts are set up automatically using **CloudFormation**, controlled by **CodePipeline** in the development account. The next step is to set up a trigger for CodePipeline. Done so using Event-Bridge. So when a model version is approved in model registry, this will generate an event which can be used to trigger deployment via CodePipeline. So now everything's connected.![[Attachments/Pasted image 20240714221047.png]]
- Model monitor goal is to monitor ML models in production and to detect a change in behaviour or accuracy. To start, I enabled **data capture** on the endpoints in the staging and production accounts. This captures the incoming requests and outgoing inference results and stores them in S3 buckets. If you have a model monitoring use case, which doesn't require labelling the incoming requests, then you could run the whole process directly on your staging and production accounts. But in this case, I assume the data needs to be combined with labels or other data that's on the development account. So I use **S3 replication** to move the data onto an S3 bucket in the development account. Now, in order to tell if the behaviour of the model or the data has changed, we need something to compare it to. That's where the **model baseline** comes in. During the training process as part of the automated re-training pipeline, we can generate a baseline dataset, which records the expected behaviour of the data and the model. So that gives me all the components I need to set up **SageMaker model monitor**, which will compare the two datasets and generate a report. The final step in this architecture is to take action based on the results of the model monitoring report. And we can do this by sending an event to **EventBridge** to trigger the re-training pipeline when a significant change has been detected.![[Attachments/Pasted image 20240714221803.png]]

#### LARGE SETUP:
![[Attachments/Pasted image 20240714222410.png]]
- Data is stored in a separate account. It's a common strategy to have your data lakes set up in one account with fine-grained access controls to determine which datasets can be accessed by resources in other accounts. Really, the larger a company becomes the more AWS accounts they tend to use, all managed through AWS Organizations. 
- Different dev account for development and model artifact and version control.
- Automated re-training pipeline in a separate operations account. The operations account is normally used for any automated workflows which don't require manual intervention by the data scientists.
- Model registry in yet another Artifact account. It's good practice to store all of your artifacts in a separate artifact account. This is an easy way to prevent data scientists from accidentally changing production artifacts. 
- ECR is placed in the artifact account because environments, especially production environments are artifacts which need to be protected. 
- let's bring back the production and staging accounts with the deployment setup. This is exactly the same as in the previous architecture. The infrastructure in the production and staging accounts is still set up automatically through **CloudFormation** in **CodePipeline**, but CodePipeline sits in a CI/CD account. 
- Connect model registry to CodePipeline by using EventBridge. And now we have all the pieces connected again. 
- There's one change I want to make to the use of ECR here. In the previous architecture diagrams, I assumed that data scientists were building Docker containers and registering these containers and ECR manually. This process can be simplified and automated using **CodeBuild** and **CodePipeline**. The data scientist or machine learning engineer can still write the Docker file, but the building and registration of the container is performed automatically. 
- Model monitor to trigger model re-training if significant changes in model behaviour were detected. So let's bring that back as well, starting with the data capture in the staging and production accounts, followed by data replication into the operations account. As before, model monitor will need a baseline to compare performance and the generation of this baseline can be a step in SageMaker Pipelines. 
- Finally, I'll bring back model monitor to generate reports on drift and trigger re-training if necessary. 
- SageMaker feature store, which sits in the artefact account because features are artifacts which can be reused. Data scientists will normally perform feature engineering before training a model, and it has a large impact on model performance. In large companies, there's a good chance that data scientists will be working on separate use cases which rely on the same dataset. A feature store allows data scientists to take advantage of features created by others. It reduces their workload and also ensures consistency in the features that are created from a dataset. 
- SageMaker Clarify can be used by data scientists in the development phase to identify bias in datasets and to generate  explainability reports for models, similar to model monitor, Clarify can also be used to generate baseline bias and explainability reports, which can then be compared to the behaviour of the model in the endpoint. If Clarify finds that bias is increasing or the explainability results are changing, it can trigger a re-training of the model.


### EDA with AWS:

- Can be done using **SageMaker Data Wrangler**, notebooks, or processing jobs:![[Attachments/Pasted image 20240715134456.png]]
- Notebooks:![[Attachments/Pasted image 20240715134648.png]]
- Processing Jobs:![[Attachments/Pasted image 20240715134735.png]]