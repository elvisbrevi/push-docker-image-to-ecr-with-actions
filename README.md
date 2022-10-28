# Build and Push Docker Image to AWS ECR Using GitHub Actions

From tutorial https://www.fosstechnix.com/build-and-push-docker-image-to-aws-ecr-using-github-actions/

In this article we are going to learn, Create Docker file for NodeJS App, Make a package.json file, Create Workflow in GitHub Actions, Create Repository AWS ECR, Create Secrets in GitHub, How to Build and Push Docker Image to AWS ECR Using GitHub Actions.

Table of Contents
Pre-Requisites:
<div id="ez-toc-container" class="ez-toc-v2_0_34 counter-hierarchy ez-toc-counter ez-toc-container-direction toc_close">
<div class="ez-toc-title-container">
<p class="ez-toc-title">Table of Contents</p>
<span class="ez-toc-title-toggle"><a href="#" class="ez-toc-pull-right ez-toc-btn ez-toc-btn-xs ez-toc-btn-default ez-toc-toggle" style="display: inline;"><label for="item" aria-label="Table of Content"><i class="ez-toc-glyphicon ez-toc-icon-toggle"></i></label><input type="checkbox" id="item"></a></span></div>
<nav><ul class="ez-toc-list ez-toc-list-level-1" style=""><li class="ez-toc-page-1 ez-toc-heading-level-2"><a class="ez-toc-link ez-toc-heading-1" href="#pre-requisites" title="Pre-Requisites:">Pre-Requisites:</a></li><li class="ez-toc-page-1 ez-toc-heading-level-2"><a class="ez-toc-link ez-toc-heading-2" href="#step1-create-docker-file-for-nodejs-app" title="Step#1:Create Docker file for NodeJS App">Step#1:Create Docker file for NodeJS App</a></li><li class="ez-toc-page-1 ez-toc-heading-level-2"><a class="ez-toc-link ez-toc-heading-3" href="#step2-make-a-packagejson-file" title="Step#2:Make a package.json file">Step#2:Make a package.json file</a></li><li class="ez-toc-page-1 ez-toc-heading-level-2"><a class="ez-toc-link ez-toc-heading-4" href="#step3-create-workflow-in-github-actions" title="Step#3:Create Workflow in GitHub Actions">Step#3:Create Workflow in GitHub Actions</a></li><li class="ez-toc-page-1 ez-toc-heading-level-2"><a class="ez-toc-link ez-toc-heading-5" href="#step-4-create-repository-aws-ecr" title="Step #4:Create Repository AWS ECR ">Step #4:Create Repository AWS ECR </a></li><li class="ez-toc-page-1 ez-toc-heading-level-2"><a class="ez-toc-link ez-toc-heading-6" href="#step-5-create-secrets-in-github" title="Step #5:Create Secrets in GitHub">Step #5:Create Secrets in GitHub</a></li><li class="ez-toc-page-1 ez-toc-heading-level-2"><a class="ez-toc-link ez-toc-heading-7" href="#step-6-build-push-docker-image-to-aws-ecr-using-github-actions" title="Step #6:Build &amp; Push Docker Image to AWS ECR Using GitHub Actions">Step #6:Build &amp; Push Docker Image to AWS ECR Using GitHub Actions</a></li></ul></nav></div>
Pre-Requisites:
Aws account
You must have knowledge of GitHub, and Git commands
Basic knowledge of GitHub actions, CI/CD, YAML
First of all you need to create new repository on GitHub or you can use your old repository also


Step#1:Create Docker file for NodeJS App
We have to make a Dockerfile for our application

FROM node:14

WORKDIR /usr/src/app

COPY package.json .
RUN npm install 
COPY . .

EXPOSE 3000

CMD ["node", "index.js"]

Step#2:Make a package.json file
We need to create a package.json using below code

{
    "name": "docker_nodejs_demo",
    "version": "1.0.0",
    "description": "",
    "main": "index.js",
    "scripts": {
      "test": "echo \"Error: no test specified\" && exit 1"
    },
    "keywords": [],
    "author": "",
    "license": "ISC",
    "dependencies": {
      "config": "^3.3.6",
      "express": "^4.17.1"
    }
  }
  
Step#3:Create Workflow in GitHub Actions
Now configure the GitHub Actions. For the Image building and pushing it to AWS ECR and here we are going to use a great tool from GitHub called GitHub Actions.

Now let’s create your workflow using below file:

name: Build and push image to ECR

on: push
  

jobs:
  
  build:
    
    name: Build Image
    runs-on: ubuntu-latest

   
    steps:

    - name: Check out code
      uses: actions/checkout@v2
    
    - name: Configure AWS credentials
      uses: aws-actions/configure-aws-credentials@v1
      with:
        aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
        aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
        aws-region: ap-south-1

    - name: Login to Amazon ECR
      id: login-ecr
      uses: aws-actions/amazon-ecr-login@v1

    - name: Build, tag, and push image to Amazon ECR
      env:
        ECR_REGISTRY: ${{ steps.login-ecr.outputs.registry }}
        ECR_REPOSITORY: github-sample
        IMAGE_TAG: sample_image
      run: |
        docker build -t $ECR_REGISTRY/$ECR_REPOSITORY:$IMAGE_TAG .
        docker push $ECR_REGISTRY/$ECR_REPOSITORY:$IMAGE_TAG

Let’s understand our workflow first

Steps: Steps represent a sequence of tasks that will be executed as part of the job steps

Job: Name: Check out code

It job simply checks out our GitHub repository for “Dockerfile” to build the docker image

Job: Name: Configure AWS credentials

In this job we need to configure AWS login Credentials. For accessing the AWS ECR we need to define a custom Role in later steps.

Job:Name: Build, tag, and push image to Amazon ECR

In this step we are going to build the Docker Image by copying using the Code in our Repository, tagging the Image with Version and pushing Image to AWS ECR 


Step #4:Create Repository AWS ECR
Firstly go to your AWS account and go to ECR and then create a repository and fill the input field and create repository

Build and Push Docker Image to AWS ECR Using GitHub Actions 1
fig 1
After creating your ECR repository, go to your workflow and edit the field ECR_REPOSITORY, and enter the name of our ECR Repository


Step #5:Create Secrets in GitHub
Now to access our AWS ECR, so here we need to add AWS secrets for AWS_ACCESS_KEY_ID and AWS_SECRET_ACCESS_KEY

So go to the settings and create secrets and set them as Environment Variables.


Note: Your IAM user has proper valid IAM permissions. Your IAM user must have “AmazonEC2ContainerRegistryFullAccess”

Build and Push Docker Image to AWS ECR Using GitHub Actions 2
fig 2

Step #6:Build & Push Docker Image to AWS ECR Using GitHub Actions
Now let’s commit and push our code into our repository

Build and Push Docker Image to AWS ECR Using GitHub Actions 3
fig 3
Now our Job success

Build and Push Docker Image to AWS ECR Using GitHub Actions 4
fig 4
Now you can also open the ECR repository and check for the final image with the latest tag inside it.

Build and Push Docker Image to AWS ECR Using GitHub Actions 5
fig 5
we have covered How to Build and Push Docker Image to AWS ECR Using GitHub Actions.

Conclusions:

In this article we have covered Create Docker file for NodeJS App, Make a package.json file, Create Workflow in GitHub Actions, Create Repository AWS ECR, Create Secrets in GitHub, How to Build and Push Docker Image to AWS ECR Using GitHub Actions.

Related Articles:

Git Branching Strategy Best Practices

Reference:

GitHub Actions AWS ECR page

