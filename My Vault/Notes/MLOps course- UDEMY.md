
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
### AWS Code Build:

- [Codebuild](https://www.youtube.com/watch?v=qGgNyOkZEb0)
- Fully managed continuous integration service that compiles source code, runs tests, produce software packages that are ready to deploy.
- Can run bet 5mins and 8hours, so can be used to make long builds, run long tests. Great candidate for running performance testing for running functional testing integration testing and so on.
- Can specify env variables, compute size, operating system, generate artifacts or not, logging in AWS CloudWatch in a build object.
- What actually happens is, a docker container will be started by codebuild and it will take all the code from the remote repository and test it according to the specification in the buildspec.yml.  build environment represents my combination of my operating system, programming, language runtime and also the tools that my code build uses to run a particular build.
- If build is successful it means that all the code we have right here in the repository is compliant and passes the test suite that we have into the buildspec.yaml.
- All the resources are created in elastic manner in a server-less manner and that makes it extremely flexible and really great building and testing engine within aws.
- Buildspec.yml
	- Collection of build commands and its related settings which are present in yml format. And these commands and the settings will be used by my codebuild in order to run a particular build.
	- Placed inside source directory or the directory where exactly my source codes are present.
	- If different file name or if it is in a separate location, manually specify where exactly buildspec.yaml file is present.
	- 4 phases:
		- Install phase: mention runtime version. Can run other commands as well.
		- Pre-build phase: Check the connection, check whether we are able to log into my system and any other pre checks that I want to do before I perform the build.
		- Build phase: Activities my code build has to perform to complete the build? Tests etc.
		- Post-build phase: cleanup activity that I want to do.
	- Artifacts: Files that I'll be generating whenever I'm performing this code build. eg in ML workflow, trained model is treated as artifact.
