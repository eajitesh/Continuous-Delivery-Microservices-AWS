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
    - If all of the above steps are successful, build the image 
    - Push the image to image repository such as Dockerhub or AWS ECR
    - Register task definition with AWS ECS
    - Update AWS ECS

## Set ECS Service 

Before configuring steps into Jenkins, following needs to be setup using AWS ECS console.

### Create a repository


## Configure Jenkins Post-steps

Jenkins post-steps can be configured to achieve following:

 1. Pushing images to Dockerhub; Register task definition; Update ECS

```
# Build the docker image
sudo docker build -t ImageName:tag /var/jenkins_home/workspace/SpringBootApp
# Login into Dockerhub
sudo docker login -u="dockerhubLogin" -p="dockerhubPassword"
# Push docker image
sudo docker push ImageName:tag
# Login using AWS CLI
yes "" | aws configure --profile default ; aws ecr get-login > awslogin.sh ; sudo sh awslogin.sh
# Register task definition`
aws ecs register-task-definition --family TaskDefinitionName --container-definitions "[{\"name\":\"TaskDefinitionName\",\"image\":\"ImageName:tag\",\"memory\":300,\"portMappings\":[{\"hostPort\":0,\"containerPort\":8080,\"protocol\":\"tcp\"}]}]" 
aws ecs update-service --cluster ClusterName --service ServiceName --task-definition TaskDefinitionName --desired-count 2
```
In above code samples, note some of the following:
 - **ImageName:tag** should be replaced with image such as ajitesh/springboot-web-app:latest. 
 - **TaskDefinitionName** should be replaced with name of the task definition provided at the time of creating task definition using AWS ECS console.
 - **ClusterName** should be replaced with name of the ECS cluster
 - **ServiceName** should be replaced with name of the service


