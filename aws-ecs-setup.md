# Continuous Delivery of Microservices with AWS ECS

[AWS EC2 Container Service (ECS)](http://docs.aws.amazon.com/AmazonECS/latest/developerguide/Welcome.html) is a highly scalable container management service which is used to start, stop and run microservices within Docker containers on AWS EC2 instances. In this project, it is demonstrated as to how to deploy container-based microservices using CLI commands from within Jenkins. In order to achieve the same, following needs to be done:

1. Setup ECS Service
- Create a repository (EC2 Repository - ECR)
- Create a task definition 
- Create an ECS cluster
- Create a service
2. Configure Jenkins Post-build steps 

Before getting into setup details, let us try and understand the solution architecture related with achieving continuous delivery of microservices with AWS ECS.

## Solution Architecture 

Following represents the solution architecture of deploying microservices using AWS ECS.

![Solution Architecture - Microservices to AWS ECS](https://github.com/eajitesh/Continuous-Delivery-Microservices-AWS/blob/master/images/aws_ecs.png)

In above diagram, pay attention to following:

1. Code is checked into code repository such as Gitlab
2. Webhook configured in GitLab triggers the Jenkins job
3. Jenkins job starts executing which results in following steps:
