# Build and Deploy Dockerized React JS Application with AWS CI-CD and ElasticBeanstalk


## Description:
Build and deploy applications with AWS native CI-CD methodologies.


# Steps:

## Application and Dockerfile Creation

Create a simple reactjs application and create Dockerfile for that application.

## Code Storage (CodeCommit)
Create a Codecommit repository in AWS and push created Local codes into Codecommit using CLI commands.

## Docker Build (CodeBuild)
Use CodeBuild service to build the code using dockerfile and push created docker image into docker hub (Use buildspec.yml file).

## Continuous Deployment (ElasticBeanstalk)
Create a ElasticBeanstalk docker based application to deploy our reactjs application.

## Continuous Integration (CodePipeline)

Create a Codepipeline and choose source as codecommit and build stage as codebuild and deploy stage as elasticbeanstalk to deploy our created Docker application. Deploy using Dockerrun.aws.json file.