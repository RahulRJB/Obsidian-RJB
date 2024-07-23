
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

- 