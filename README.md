### Steps to build the docker conatiner locally
1. Create a dockerfile first locally
2. Once the docker file is created build the docker file using the command
   docker build -t hello-world .
3. Tag the docker image to push to dockerhub
   docker tag hello-world balav8/hello-world-app:latest
4. Push it to docker hub
   docker push hello-world-app:latest

### How the cicd pipeline works
1. Once the dockerfile is ready, we can create a github-actions file.
2. Click on the actions tab and click on create your own workflow
3. Now you can add your pipeline steps
   First step is on which state your pipeline should trigger, we have set on push to main branch
   Then you need to specify the runner, we are using ubuntu-latest
   Next step is to checkout the code from the github
   Next is to build the docker image
   Next is to tag the docker image
   Next step is login to dockerhub
   Next stpe is to push the image to dockerhub
4. Now whenever the code is updated, this github actions will run and push the latest image to dockerhub and run the latest code.
5. In this way the job is triggered whenever a new code is updated.


### To deploy the container in ecs
1. You need to create a repository in ECR in AWS
2. Once you're repository is complete, view push commands and run all the commands one by one into your ec2 instance, to get your image to ECR
3. Once you're image is pushed to ECR, go to ECS, create a task definition. give it a name, provide the CPU and memory, select the launch type as AWS fargate, give your container name and image url and create it.
4. Once task definition is created, you need to create a cluster in ECS, once the cluster is created, create a service for your task, and update the deployment in ECS.
5. Run the below deployment script, you can create a shell script and add the below steps


#!/bin/bash

# Variables
CLUSTER_NAME="webapps"
SERVICE_NAME="webapps"
TASK_DEFINITION_NAME="taskdefinition"
IMAGE_TAG="latest" # or specify a specific tag for your Docker image

# Build your Docker image (replace with your build command)
docker build -t hello-world-app .
# Tag your Docker image for Amazon ECR
docker tag hello-world-app:latest aws_account_id.dkr.ecr.region.amazonaws.com/your-repository-name:latest
# Login to Amazon ECR
aws ecr get-login-password --region region | docker login --username AWS --password-stdin aws_account_id.dkr.ecr.region.amazonaws.com
# Push your Docker image to Amazon ECR
docker push aws_account_id.dkr.ecr.region.amazonaws.com/your-repository-name:latest

# Register the task definition
aws ecs register-task-definition --cli-input-json file://taskdefinition.json

# Update the ECS service to use the new task definition
aws ecs update-service --cluster $CLUSTER_NAME --service $SERVICE_NAME --task-definition $TASK_DEFINITION_NAME



PS:
Also attaching task definition json separately
