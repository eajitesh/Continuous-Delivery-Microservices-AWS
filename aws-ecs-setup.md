# Continuous Delivery of Microservices with AWS ECS

[AWS EC2 Container Service (ECS)](http://docs.aws.amazon.com/AmazonECS/latest/developerguide/Welcome.html) is a highly scalable container management service which is used to start, stop and run microservices within Docker containers on AWS EC2 instances. In this project, it is demonstrated as to how to deploy container-based microservices using CLI commands from within Jenkins. In order to achieve the same, following needs to be done:

 1. Setup ECS Service
    - Create a repository (EC2 Repository - ECR)
    - Create a task definition 
    - Create an ECS cluster
    - Create a service
 2. Configure Jenkins build Post-steps 

**Note**: For the demonstration purpose, both **Gitlab** and **Jenkins** are setup within **Docker Containers**. In real world scenario, Gitlab and Jenkins may get setup within different VMs.

Before getting into the setup details, let us try and understand the solution architecture related with achieving continuous delivery of microservices with AWS ECS.

## Solution Architecture 

Following represents the solution architecture of deploying microservices using AWS ECS.

![Solution Architecture - Microservices to AWS ECS](https://github.com/eajitesh/Continuous-Delivery-Microservices-AWS/blob/master/images/aws_ecs.png)

In above diagram, pay attention to some of the following:

 1. Code is checked into code repository such as Gitlab
 2. Webhook configured in GitLab triggers the Jenkins job
 3. Jenkins job starts executing which results in following steps:
    - Retrieve the microservice artifacts from Gitlab
    - Build the microservice
    - Run the tests such as unit and integration tests
    - Build the image if all of the above steps are successful
    - Push the image to image repository such as Dockerhub or AWS ECR
    - Register task definition with AWS ECS
    - Update AWS ECS

## Set ECS Service 

Before configuring steps into Jenkins, following needs to be setup using AWS ECS console.

### Create an Image Repository with AWS ECR

First step is getting setup with AWS ECR. Following command needs to be executed in order to create an ECR repository.
```
# Login into ECR
aws configure
aws ecr get-login
docker built -t ImageName .
docker tag ImageName:tag AWS_ECR_URL/ImageName:tag
docker push AWS_ECR_URL/ImageName:tag
```
Note some of the following with above command:
 - **AWS_ECR_URL** is of the format https://__aws_account_id__.dkr.ecr.__region__.amazonaws.com. One can get the value of Account id by logging into console and going to My Account page. Region information can be found from the [region and availability zones page](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-regions-availability-zones.html)
 - **aws configure** command is used to setup AWS CLI installation. The command would require one to enter credentials and config information before one starts working with their AWS services. The command would require one to enter details for access key ID, secret access key, default region name and default output format. However, as we need to achieve this without entering details at the prompt, following needs to be done in order to achieve the promptless command such as **aws configure --profile default**
    - Create a folder, **.aws** in the home.
    - Create a file named as **config** within above folder, **.aws**, with following content. One could get access key id and secret access key information by logging into the AWS console and accessing "My Security Credentials".
```
[default]
aws_access_key_id=AKXXIXXYXX4X4XXXXJRY
aws_secret_access_key=DyxxxxxxeqQyxyyyyytXcwwthbbCxaaaa8Qi0y
region=us-west-2
output=json
```
 - **aws ecr get-login** command is used to get the login prompt which needs to be executed further to login (start authenticated session) into AWS ECR and thereafter, push the image.
 - Other commands are usual commands to push the docker image to the AWS ECR image repository.

Executing above commands leads to user entering the details at the prompt. If one wants to achieve the same without prompt, from within Jenkins, the same could be achieved using following command which is a combination of commands to achieve promptless execution. 

```
yes "" | aws configure --profile default ; aws ecr get-login > awslogin.sh ; sudo sh awslogin.sh
```
One can observe that executing command such as "aws ecr get-login" leads to output of command such as following which needs to be executed for successfully logging in. The command below is sent to awslogin.sh file as shown in the command and then awslogin.sh is executed. 
```
docker login -u AWS -p SomeRandomPasswordStringSentByAWS -e none https://**aws_account_id**.dkr.ecr.**region**.amazonaws.com
```

### Create a Task Definition

Next step is to create a task definition. Command such as following could be used to create the task definition:

```
aws configure --profile default # Read the details under Create an Image Repository
aws ecs register-task-definition --family TaskDefinitionName --container-definitions "[{\"name\":\"TaskDefinitionName\",\"image\":\"ImageName:tag\",\"memory\":300,\"portMappings\":[{\"hostPort\":0,\"containerPort\":8080,\"protocol\":\"tcp\"}]}]" 
```
In above command, note the following two aspects:
 - **TaskDefinitionName** which is the name of the task definition that one needs to provide
 - **container-definitions** which is used to provide information related with one or more containers which will be started as a result of executing task based on the task definition.

One may want to login  and access the AWS console at Services/EC2 Container Service/Task Definitions and try and create task definition to understand different aspects of task definition creation.

### Create an ECS Cluster

As this is one time activity, one may want to use AWS console at Services/EC2 Container Service/Clusters to create the cluster. It is pretty straight forward and very easy to create the cluster.

### Create/Update the Service

Once done with above steps


## Configure Jenkins Post-steps

Jenkins post-steps can be configured to achieve following:

### Pushing images to Dockerhub; Register task definition; Update ECS

```
# Build the docker image
sudo docker build -t ImageName:tag /var/jenkins_home/workspace/SpringBootApp

# Login into Dockerhub; dockerhubLogin and dockerhubPassword is login and password respectively for dockerhub.com
sudo docker login -u="dockerhubLogin" -p="dockerhubPassword"

# Push docker image into Dockerhub
sudo docker push ImageName:tag

# Login using AWS CLI
yes "" | aws configure --profile default ; aws ecr get-login > awslogin.sh ; sudo sh awslogin.sh

# Register task definition`
aws ecs register-task-definition --family TaskDefinitionName --container-definitions "[{\"name\":\"TaskDefinitionName\",\"image\":\"ImageName:tag\",\"memory\":300,\"portMappings\":[{\"hostPort\":0,\"containerPort\":8080,\"protocol\":\"tcp\"}]}]" 

# Update service
aws ecs update-service --cluster ClusterName --service ServiceName --task-definition TaskDefinitionName --desired-count 2
```
In above code samples, note some of the following:
 - **ImageName:tag** should be replaced with image such as ajitesh/springboot-web-app:latest. 
 - **TaskDefinitionName** should be replaced with name of the task definition provided at the time of creating task definition using AWS ECS console.
 - **ClusterName** should be replaced with name of the ECS cluster
 - **ServiceName** should be replaced with name of the service

### Pushing images to ECR; Register the task definition ; Update the ECS service
```
# Login into AWS
yes "" | aws configure --profile default ; aws ecr get-login > awslogin.sh ; sudo sh awslogin.sh

# Build the docker image
sudo docker build -t ImageName /var/jenkins_home/workspace/SpringBootApp

# Tag the image; Push docker image into AWS ECR
sudo docker tag ImageName:tag 153819127898.dkr.ecr.us-west-2.amazonaws.com/ImageName:tag
sudo docker push AWS_ECR_URL/ImageName:tag

# Register task definition`
aws ecs register-task-definition --family TaskDefinitionName --container-definitions "[{\"name\":\"TaskDefinitionName\",\"image\":\"AWS_ECR_URL/ImageName:Tag\",\"memory\":300,\"portMappings\":[{\"hostPort\":0,\"containerPort\":8080,\"protocol\":\"tcp\"}]}]" 

# Update service
aws ecs update-service --cluster ClusterName --service ServiceName --task-definition TaskDefinitionName --desired-count 2
```

