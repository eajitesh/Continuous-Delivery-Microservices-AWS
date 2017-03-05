# Setup AWS ECS

[AWS EC2 Container Service (ECS)](http://docs.aws.amazon.com/AmazonECS/latest/developerguide/Welcome.html) is a highly scalable container management service which is used to start, stop and run microservices within Docker containers on AWS EC2 instances. In this project, it is demonstrated as to how to deploy container-based microservices using CLI commands from within Jenkins. As part of getting setup with ECS, following needs to be done:

1. Create a repository (EC2 Repository - ECR)
2. Create a task definition 
3. Create an ECS cluster
4. Create a service


## Solution Architecture 

Following represents the solution architecture of moving microservices to AWS ECS service.

![Solution Architecture - Microservices to AWS ECS](https://github.com/eajitesh/Continuous-Delivery-Microservices-AWS/blob/master/images/aws_ecs.png)
