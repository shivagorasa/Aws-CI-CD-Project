Let's use the following source code repo to our project https://github.com/shivagorasa/Aws-CI-CD-Project.git

Create a Docker file for above project using following Dockerfile

```
# Using official Nginx image as the base image
FROM nginx:1.19.10

COPY nginx.conf /etc/nginx/nginx.conf

# Copy our web application files into the Nginx document root
COPY . /var/www/html 

WORKDIR /var/www/html -

# Expose port 80 to the outside world
EXPOSE 80  

CMD ["nginx", "-g", "daemon off;"]
```



To implement the task above we can setup continuous integration with aws codepipeline.

## Set up CodeCommit:

 * Go to the AWS Management Console and navigate to CodeCommit.
 * Create a new repository or use an existing one to store our application code.

 * Created a repo in code commit named Demo-web-app-Repo 

 * Logged in with gitbash with aws credentials of git

 * Used cli for uploading source code to codecommit repo 


![image](https://github.com/shivagorasa/Aws-CI-CD-Project/assets/97184376/0d3f561b-5ce0-42c2-b473-ccdcef6a71dc)


## Create CodeBuild Project:

 * Go to the CodeBuild console.
 * Create a new CodeBuild project.
 * Configure the source as your CodeCommit repository.
 * Choose the appropriate runtime environment with Docker support.
 * Specify the buildspec.yml file that defines how to build your Docker image.
 * Ensure that your build artifacts include the Docker image.

 * Create a build called web-app-build source provider as aws code commit
   Leave every thing in env as default and change service role or create a new service role 

 * Under environment add env variables dockerhub username and docker hub password
 * Under build spec use a buildspec.yml file which is present in our source code  repo on code commit
 * Then create build project 

 ### buildspec.yml:

 ```
 version: 0.2

phases:
  install:
    runtime-versions:
      docker: 19

  pre_build:
    commands:
      - echo Logging in to Docker Hub...
      - echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin

  build:
    commands:
      - echo Building Docker image...
      - docker build -t my-docker-image .
      - docker tag my-docker-image $DOCKER_USERNAME/my-docker-image

  post_build:
    commands:
      - echo Pushing Docker image to Docker Hub...
      - docker push $DOCKER_USERNAME/my-docker-image
 ```
 ### CodebuildProject output

![image](https://github.com/shivagorasa/Aws-CI-CD-Project/assets/97184376/29e1244f-4119-4806-879e-7bcaa531fe4b)


## If we click on start build , a docker image is pushed to our docker hub registry with username/my-docker-image:latest

![image](https://github.com/shivagorasa/Aws-CI-CD-Project/assets/97184376/892e4db9-4f63-4a8c-bff3-a91e6771dfd8)


![image](https://github.com/shivagorasa/Aws-CI-CD-Project/assets/97184376/2731bc86-f50b-4c8f-b39c-46bf2b555897)


## Continuous Deployment with Elastic Beanstalk:

Prepare Dockerrun.aws.json:

* Create a Dockerrun.aws.json file in the root directory of your project. This file specifies how to run your Docker container on Elastic Beanstalk.

```
{
  "AWSEBDockerrunVersion": "1",
  "Image": {
    "Name": "shivagorasa/my-docker-image",
    "Update": "true"
  },
  "Ports": [
    {
      "ContainerPort": 80
    }
  ]
}
```

### Create Elastic Beanstalk Environment:

* Go to the Elastic Beanstalk console.
* Create a new Docker-based environment.
* Upload or specify the location of your Dockerrun.aws.json file.
* Configure any other environment settings as needed.

Create a ElasticBeanstalk docker based application to deploy our reactjs application.

Choose create application -> applicaton name 

Then select application name and select application environment

Choose a sample docker application 

Choose above service role and choose a key to login to a instance , choose vpc, for instance and traffic scaling 
Select single instance , use default under monitoring and create application 

We get default docker application running on elastic bean stalk

![image](https://github.com/shivagorasa/Aws-CI-CD-Project/assets/97184376/fd0bd936-1b83-4896-b19d-5875c9c0f941)



**Later on elastic beanstalk upload source code as a zip file with buildspec.yml and Dockerrun.aws.json in the sourcecode directory** 

Using above create a Elastic Beanstalk TO DEPLOY OUR APPLICATION , IF SUCCESSFUL WE GET FOLLOWING UNDER LOGS: 

![image](https://github.com/shivagorasa/Aws-CI-CD-Project/assets/97184376/be4717cf-f040-4119-86a7-7e2d534bb8af)


Instance created by Elastic beanstalk
![image](https://github.com/shivagorasa/Aws-CI-CD-Project/assets/97184376/ad582e8b-d4b1-4c43-86e8-c10ebabd6da2)


WE CAN ACCESS OUR APP RUNNING USING DOMAIN UNDER Elastic beanstalk 

![image](https://github.com/shivagorasa/Aws-CI-CD-Project/assets/97184376/f958f269-acfa-4944-bb9e-ac3cbec193ae)


## Set up CodePipeline:

* Go to the CodePipeline console.
* Create a new pipeline.
* Configure the source stage to use CodeCommit as the source provider.
* Configure the build stage to use CodeBuild as the build provider.
* Specify the CodeBuild project you created earlier.
* Optionally, configure any additional stages for testing or approval.

In Code pipeline Select source as code commit, artifacts like source code is sent to aws s3 bucket, for build process select build as code build, and finally for code deploy use aws elastic beanstalk with required IAM role and policies, if everything works well, we get following 

![image](https://github.com/shivagorasa/Aws-CI-CD-Project/assets/97184376/2ae262a1-5359-4c03-bfea-f10b9bc70dca)


And similarly, we use domain on elastic beanstalk to access our application running on aws 

![image](https://github.com/shivagorasa/Aws-CI-CD-Project/assets/97184376/98dfa937-f062-411a-8840-a3695cc56dca)


Conclusion: Hence with proper rules and policies attached to our pipeline we will be able to achieve successful code pipeline build with AWS 

Submitted by __Shiva Kumar Gorasa__


