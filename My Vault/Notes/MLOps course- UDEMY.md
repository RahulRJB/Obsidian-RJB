
# MLOps course- UDEMY


DATE:  17-07-24


Tags: [[Notes/MLOps AWS|MLOps AWS]] [[Notes/AWS|AWS]]

# References:

[Udemy link](https://tcsglobal.udemy.com/course/practical-mlops-for-data-scientists-devops-engineers-aws/learn/lecture/38044170#content)   [Slides](https://workdrive.zohopublic.in/file/nbo915bcd914fd5384166ba152ff6ec0bf601)



# Content:

### Intro:
- ![[Attachments/Pasted image 20240717131811.png]]
- ![[Attachments/Pasted image 20240717131910.png]]
- ![[Attachments/Pasted image 20240718115124.png]]
- ![[Attachments/Pasted image 20240718115245.png]]
- ![[Attachments/Pasted image 20240718115822.png]]
### AWS Code Commit:

- ![[Attachments/Pasted image 20240718140112.png]]
- The userid/password is present in IAM->User->{select user}-> Security creds->HTTPS GIT creds for codecommit. This is required when we want to access the remote repo from local.

### AWS Code Build:

- [Codebuild](https://www.youtube.com/watch?v=qGgNyOkZEb0)
- Fully managed continuous integration service that compiles source code, runs tests, produce software packages that are ready to deploy.
- Can run bet 5mins and 8hours, so can be used to make long builds, run long tests. Great candidate for running performance testing for running functional testing integration testing and so on.
- Can specify env variables, compute size, operating system, generate artifacts or not, logging in AWS CloudWatch in a build object.
- What actually happens is, a docker container will be started by codebuild and it will take all the code from the remote repository and test it according to the specification in the buildspec.yml.  build environment represents my combination of my operating system, programming, language runtime and also the tools that my code build uses to run a particular build.
- If build is successful it means that all the code we have in the repository is compliant and passes the test suite that we have into the buildspec.yaml.
- All the resources are created in elastic manner in a server-less manner and that makes it extremely flexible and really great building and testing engine within AWS.
- #### Buildspec.yml
	- Collection of build commands and its related settings which are present in yml format. And these commands and the settings will be used by my codebuild in order to run a particular build.
	- Placed inside source directory or the directory where exactly my source codes are present.
	- If different file name or if it is in a separate location, manually specify where exactly buildspec.yaml file is present.
	- 4 phases:
		- Install phase: mention runtime version. Can run other commands as well.
		- Pre-build phase: Check the connection, check whether we are able to log into my system and any other pre checks that I want to do before I perform the build.
		- Build phase: Activities my code build has to perform to complete the build? Tests etc.
		- Post-build phase: cleanup activity that I want to do.
	- Artifacts: Files that I'll be generating whenever I'm performing this code build. eg in ML workflow, trained model is treated as artifact.
- #### Env variables:
	- Can be specified at individual builds in the console. These are variables that may change over time so not specified in the code itself. Increases dynamicity.
	- If we want to do a quick testing with a different configuration, we can use existing project and can override a particular values in runtime, env variables etc, as per requirement and I can test out with a different version of my build.
	- AWS secret manager and AWS Systems manager Parameter Store can be used to store credentials, parameters ect.
	- If we want to use the values that are stored in my parameter store, we should make sure that codebuild role is having the necessary access to access this AWS Systems Manager.
- #### Artifacts:
	- Can be specified in the console, if we want to save artifacts or not in S3. and whatever needs to be saved needs to be specified in the artifact section in buildspec.yml.  Let's say we have trained a ML algorithm and there are some parameters that you have learned from your given data. We can store the learned parameters as a artifact, you can save that artifact into your S3 bucket. Or if we have built your software application, and once the software application has been built, you want to create a text file with all the information of the software application that you that you have just built. So you have created that text file during your runtime i.e codebuild.  Now you want to save that configuration file in the S3 bucket so that you will not lose that information. In such scenarios, we can use the section of artifacts and specify the files that you want to save once the code build activity is complete.
- ![[Attachments/Pasted image 20240723115040.png]]
- #### Triggers:
	- We can automate in a way that when do any code changes to my code commit repository, it will pull the latest code from the whatever that is in my source code and it will perform the codebuild.
	- Done using AWS EventBridge.
### AWS EvenBridge:

- Can be triggered on a periodic basis or it could be based on a trigger as well.
- Rules>Create Rule>Rule with event pattern or Schedule>Select the source> Select the target event.
### AWS Code Deploy:

- Fully managed service
- Coordinates the application deployment and updates it across the entire fleet of AWS EC2 instances of any size.
- Help us to automate the software deployments where the applications can be running on EC2 instances or it can be running on AWS Fargate, which is a serverless architecture, AWS Lambda or on-premise servers. Supports server as well as the serverless, including the container deployments.
- No downtime- Can specify how does the deployment of an application should look like.
- Auto scaling.
- Rollback support.
- CodeDeploy, uses appspec.yml file.
	- ![[Attachments/Pasted image 20240724015409.png]]
	- ![[Attachments/Pasted image 20240724015733.png]]
	- ![[Attachments/Pasted image 20240724020012.png]]
	- ![[Attachments/Pasted image 20240724020239.png]]
	- ![[Attachments/Pasted image 20240724020252.png]]
	- Contains the commands to execute at each phase of deployment. Any application deployment, it's not just switch off and switch on, it contains various phases. You have to control the traffic, ensure that you stop the application, bring up the new application, stop the connection to the database, once your application is updated, then you have to do the reconnection to those application, perform some testing. All those complex activities we will be specifying inside this application configuration file.
	- 2 types of deployment: 
		- In-Place: Updating the version of the application on the existing EC2 instances. Has Downtime present. Preferred in Dev and Staging, cheaper
		- Blue Green: gradually replace the existing instance with instances running new version. No downtime. Preferred in prod.
	- Specify to which server that I want to place with the help of the deployment group, i.e which set of EC2 instances that I want to push my latest code to.
- Deployment via CodeDeploy 5 step process:
	- 1. Create an EC2 instances with Iam roles attached to it. Now this role should have the permission to read an S3 bucket. Because we have to read the source code from the S3 bucket.
	- 2. To deploy application, first create an application in the code deploy.
	- 3. Push the code revision to my S3 bucket, which is an version managed S3 bucket.
	- 4. Specify the deployment group. Decides which instances to deploy and how to deploy and which version to deploy.
	- 5. Deploy the application into an EC2 instances.
	- ![[Attachments/Pasted image 20240724011936.png]]
	- Individual  EC2 instances have code deploy agent. Whenever any new request comes in for this code deploy service, this request will be passed to the code deploy agent, if we have pushed the new version code to my repository, could be an S3 bucket. Then this code deploy agent is going to initiate a request to that s3 bucket. So as a result of this, once the request is received, the code deploy agent running inside this EC2 instance will pick the latest version of the code from this S3 bucket. In order to do this, that's the reason we need to assign an Iam role for this  EC2 instance.![[Attachments/Pasted image 20240724012454.png]]
	- ![[Attachments/Pasted image 20240724012909.png]]
	- https://github.com/manifoldailearning/mlops-with-aws-datascientists/blob/main/Section-9-CodeDeploy/CODEDEPLOY.md

### AWS Code Pipeline:

- ![[Attachments/Pasted image 20240724021218.png]]![[Attachments/Pasted image 20240724021309.png]]![[Attachments/Pasted image 20240724021419.png]]

### Sagemaker:

- ![[Attachments/Pasted image 20240724123723.png]]
### Sagemaker Feature Engg.(Preprocessing):
- #### Data Wrangler for Preprocessing:
	- Used for Feature Engg. 
	- Visual way, gives in a graph format
	- We used it to Join tables
	- Can be used to perform Analysis on data, univariate, bivariate etc. Like Histogram, Bias report, Table summary(mean, median, count etc), Target leakage, custom plots, etc.
	- Data transformation, balance imbalance data(SMOTE, random oversample, random undersmaple), drop col, handling outliers(remove, clipping), scaling num features, etc
	- Custom Transform using python/pyspark scripts
	- Encoding cat features(ordinal, one-hot)
	- Now this transformed data can be saved back to S3.
	- We can create processing jobs in data wrangler to automate this entire data pre-processing/transformation. We can specify the instance type to run the processing on etc.
	- After creating the jobs, it can be found in Sagemaker>Processing Job tab. The jobs are run using docker containers image.
- #### Notebook Preprocessing(Sagemaker commands):
	- ![[Attachments/Pasted image 20240807125050.png]]
	- ![[Attachments/Pasted image 20240807125135.png]]
	- ![[Attachments/Pasted image 20240807125003.png]]
- #### Sagemaker Preprocessing:
	- ![[Attachments/Pasted image 20240807125513.png]]
	- **The runner file, sands make-step, sands run step are custom processing jobs only.**
	- 2 imp contracts we have to follow, input and the output should be mentioned in two different directories.
		- opt/ml/processing/input - input data
		- opt/ml/processing/output - output data
	- Sagemaker processing will automatically load the input data from my S3 and uploads the transformed data back into my S3 bucket, especially after the job is completed.  Used to process terabytes of data in a sagemaker managed cluster, which is separate from the instance which is currently running the notebook server.
	- In typical workflow, the notebooks are generally used for prototyping purpose, and this is generally run on relatively inexpensive and less powerful systems. Now whenever we are performing the processing, training and model hosting at that time we'll be interested in hosting on more powerful sagemaker managed instances.
	- Code:
		- ![[Attachments/Pasted image 20240807131022.png]]Uploading data to s3 bucket first
		- ![[Attachments/Pasted image 20240807131117.png]]
		- ![[Attachments/Pasted image 20240807131231.png]]![[Attachments/Pasted image 20240807131433.png]]
		- ![[Attachments/Pasted image 20240807131707.png]]https://github.com/manifoldailearning/mlops-with-aws-datascientists/blob/main/Section-13-Feature-Engineering/feature-engg-script.py The actual preprocessing script that is run using the sagemaker processing job.
		- The processing jobs, can be found in Sagemaker>Processing Job tab.
### Sagemaker Feature Store:

- Central place to store all the generated features, for everyones use.
- We pick up the raw data and apply the feature processing technique. We transform such raw data into a meaningful feature for better modelling. Feature store is then used to store, discover and share the features for machine learning. So we will use that transform data, post it into a feature store so that we can store it in a much efficient way.
- ![[Attachments/Pasted image 20240807133524.png]]
- ![[Attachments/Pasted image 20240807133815.png]]
- ![[Attachments/Pasted image 20240807133836.png]]
- ![[Attachments/Pasted image 20240807133922.png]]
- ![[Attachments/Pasted image 20240807134016.png]]![[Attachments/Pasted image 20240807134051.png]]![[Attachments/Pasted image 20240807134141.png]]
- ![[Attachments/Pasted image 20240807134156.png]]
- 