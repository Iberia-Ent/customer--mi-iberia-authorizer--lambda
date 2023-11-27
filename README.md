# Workflow that allow you to build and deploy java maven application in a lambda function.

## Summary

Workflow that allow you to build and deploy java maven application in a lambda function.

![JAVA CICD WORKFLOW](https://user-images.githubusercontent.com/87304455/221520133-61896945-f5ec-41d7-a425-e412c8f7d291.png)

## Usage pipeline
Call directly from your workflow file like:
	
	jobs:    
	  JAVA_LAMBDA:
		uses: Iberia-Ent/software-engineering--workflow-java-maven-lambda/.github/workflows/ci-cd-java-maven-lambda.yml@v1.0
		secrets:
			AWS_DEPLOYMENT_ROLE: ${{ secrets.AWS_DEPLOYMENT_ROLE }}
			AWS_CODEARTIFACT_ROLE: ${{ secrets.AWS_CODEARTIFACT_ROLE }}
				
				
### Note: You can choose the pipeline version number, branch name or commit hash. We recommend using version number.

## Set up java maven pipeline

### Pipeline files hierarchy 

You will use the git flow for upload the package and deploying the application.

You should have this files:

* Pom.xml
* .github/config/ci-config.json
* .github/config/settings.xml
* .github/workflows/ci-cd-java-maven-lambda.yml

You should modify this files:

* Pom.xml
* .github/config/ci-config.json

Review your pom because you should have defined this elements:
- finalName (we will use this name for registering your application into CodeDeploy and S3)
- artifactId
- packaging (lowercase: war | ear | jar)
- version

#### IMPORTANT: Don´t modify .github/config/settings.xml, you can add whatever you need but don´t overwrite it because the pipeline will stop working.

### Set up ci-config.json file
#### Variables
	
	
| Variable | Required | Type | 
| -------- | -------- | ---- | 
| AWS_CODEARTIFACT_ACCOUNT_ID  | yes  | string  | 
| AWS_CODEARTIFACT_DOMAIN  | yes  | string  |
| AWS_CODEARTIFACT_REPOSITORY_CENTRAL_NAME  | yes  | string  |
| AWS_CODEARTIFACT_REPOSITORY_CENTRAL_URL  | yes  | string  |
| AWS_CODEARTIFACT_LIBRARY_REPOSITORY_NAME  | no  | string  |
| AWS_CODEARTIFACT_LIBRARY_REPOSITORY_URL  | no  | string  |
| AWS_REGION  | yes  | string  |
| BUCKET_REPOSITORY  | yes  | string  |
| JAVA_JDK_VERSION  | yes  | string  |
| JAVA_DISTRIBUTION  | yes  | string  |
| MODULE_BUILDER_FOLDER_NAME  | yes  | string  |
| DEPLOYMENT_URL  | yes  | string  |
| MAVEN_PROFILE  | yes  | string  |
| LAMBDA_FUNCTION_NAME  | yes  | string  |
| DEPLOY_ALIAS_NAME  | yes  | string  |
			

#### BUCKET_REPOSITORY we follow a naming convention so it looks like: iberia-java-releases-store-[Environment]-[BusinessDomain]. 
- Environment: integration, preproduction or production.
- BusinessDomain: backoffice, operations, commercial, cargo

For setting up the ci-config.json file with your custom properties of your project, go to your working feature branch and make the changes that you need using the above variables for example: 

	{
		"int":	{
			"AWS_CODEARTIFACT_ACCOUNT_ID": "329066851743",
			"AWS_CODEARTIFACT_DOMAIN": "iberia",		
			"AWS_CODEARTIFACT_REPOSITORY_CENTRAL_NAME": "maven-central-store",
			"AWS_CODEARTIFACT_REPOSITORY_CENTRAL_URL": "https://iberia-329066851743.d.codeartifact.eu-west-1.amazonaws.com/maven/maven-central-store/",
			"AWS_CODEARTIFACT_LIBRARY_REPOSITORY_NAME": "java-library-store",
			"AWS_CODEARTIFACT_LIBRARY_REPOSITORY_URL":"https://iberia-329066851743.d.codeartifact.eu-west-1.amazonaws.com/maven/java-library-store/",
			"AWS_REGION": "eu-west-1",
			"BUCKET_REPOSITORY": "iberia-java-releases-store-integration-operations",
			"JAVA_JDK_VERSION": "8",
			"JAVA_DISTRIBUTION": "temurin",
			"MODULE_BUILDER_FOLDER_NAME":"NA",	
			"DEPLOYMENT_URL": "https://www.miweb.dev.com",
			"MAVEN_PROFILE": "",
			"LAMBDA_FUNCTION_NAME": "myLambda",
			"DEPLOY_ALIAS_NAME": "myAlias"
		},
		"pre": {
			"AWS_CODEARTIFACT_ACCOUNT_ID": "329066851743",
			"AWS_CODEARTIFACT_DOMAIN": "iberia",		
			"AWS_CODEARTIFACT_REPOSITORY_CENTRAL_NAME": "maven-central-store",
			"AWS_CODEARTIFACT_REPOSITORY_CENTRAL_URL": "https://iberia-329066851743.d.codeartifact.eu-west-1.amazonaws.com/maven/maven-central-store/",
			"AWS_CODEARTIFACT_LIBRARY_REPOSITORY_NAME": "java-library-store",
			"AWS_CODEARTIFACT_LIBRARY_REPOSITORY_URL":"https://iberia-329066851743.d.codeartifact.eu-west-1.amazonaws.com/maven/java-library-store/",
			"AWS_REGION": "eu-west-1",
			"BUCKET_REPOSITORY": "iberia-java-releases-store-preproduction-operations",
			"JAVA_JDK_VERSION": "8",
			"JAVA_DISTRIBUTION": "temurin",
			"MODULE_BUILDER_FOLDER_NAME":"NA",	
			"DEPLOYMENT_URL": "https://www.miweb.pre.com",
			"MAVEN_PROFILE": "",
			"LAMBDA_FUNCTION_NAME": "myLambda",
			"DEPLOY_ALIAS_NAME": "myAlias"
		},
		"prod": {
			"AWS_CODEARTIFACT_ACCOUNT_ID": "329066851743",
			"AWS_CODEARTIFACT_DOMAIN": "iberia",		
			"AWS_CODEARTIFACT_REPOSITORY_CENTRAL_NAME": "maven-central-store",
			"AWS_CODEARTIFACT_REPOSITORY_CENTRAL_URL": "https://iberia-329066851743.d.codeartifact.eu-west-1.amazonaws.com/maven/maven-central-store/",
			"AWS_CODEARTIFACT_LIBRARY_REPOSITORY_NAME": "java-library-store",
			"AWS_CODEARTIFACT_LIBRARY_REPOSITORY_URL":"https://iberia-329066851743.d.codeartifact.eu-west-1.amazonaws.com/maven/java-library-store/",
			"AWS_REGION": "eu-west-1",
			"BUCKET_REPOSITORY": "iberia-java-releases-store-production-operations",
			"JAVA_JDK_VERSION": "8",
			"JAVA_DISTRIBUTION": "temurin",
			"MODULE_BUILDER_FOLDER_NAME":"NA",
			"DEPLOYMENT_URL": "https://www.miweb.Pro.com",
			"MAVEN_PROFILE": "",
			"LAMBDA_FUNCTION_NAME": "myLambda",
			"DEPLOY_ALIAS_NAME": "myAlias"
		}
	}

#### NOTE: you can use different profiles for compiling your code, depends of the environment. Use the MAVEN_PROFILE property in ci-config file and don´t forget define it in your pom.
#### NOTE2: if your are using modules and your main pom is located in a specific folder instead of in the root path, use the MODULE_BUILDER_FOLDER_NAME variable to indicatethe folder name.

## Usage git flow
![Git Flow](https://github.com/Iberia-Ent/software-engineering--git-flow--template/assets/87304455/51d5f609-d814-4ce7-9f05-4af3fc4e0e43)

See more details about how to use git-flow and configure your repository: [here](https://github.com/Iberia-Ent/software-engineering--git-flow--template)

### Deploy your application

- Create a feature working branch. For example: feat/menu
- Add your changes or commit it into your working branch.
- Push your commits to remote repository. 
- Each commit that you upload to remote repository, the pipeline is going to execute and compile your code, so make sure and check your execution before create a Pull Request to develop branch.
- For uploading your code into integration environment, open a "Pull Request", select base branch: develop and compare branch: feat/menu (working feature branch created for yourself).
- You have to wait that every status checks are sucessfully and when it's happen just confirm your changes and the merge request.
- Go to the "Action" tab and see if it has finished sucessfully or not te pipeline execution. If it has finished sucessfully your code are deployed into Integration environment.
- If you want to continue and deploy to preproduction environment, open a "Pull Request", select base branch: staging and compare branch: develop.  
- Go to the "Action" tab and see if it has finished sucessfully or not te pipeline execution. If it has finished sucessfully your code are deployed into Preproduction environment and check the tag created automatically because you should create the release from that tag when you want to deploy to production.
- For deploying to production go to "tags" tab and create the release.
- Go to "Actions" tab and see the status of the execution of the pipeline, if it was sucessfully, your code are in production environment.
#### NOTE: For deploying into Production environment the owner team has to approve your deploy, so the pipeline will be stopped until that approval.  

- Move your changes into Main branch so create a "Pull Request" like:
      - Base branch: main 
      - Compare branch: staging
        
- Delete your custom branch. Example: feat/menu

## Testing

Explanation on how to run tests

## Contributing

Briefly explains how your team members or others can contribute to the project

For the contribution and workflow guide, see [CONTRIBUTING.md](./CONTRIBUTING.md).

## Maintainers

Contact information of the team member whose is responsible for the project, see [CONTRIBUTING.md](./CONTRIBUTING.md).
