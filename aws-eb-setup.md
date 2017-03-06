# Continuous Delivery of Microservices on AWS using AWS Elastic Beanstalk

[AWS Elastic Beanstalk](https://aws.amazon.com/documentation/elastic-beanstalk/) service can be used to deploy and scale applications and services (from docker containers) developed with Java, .NET, PHP, Nodejs, Python, Ruby, Go etc on servers such as Apache, Tomcat, Nginx etc. Docker containers provide the flexibility of selecting one's runtime environment of choice including platform, programming language, app/services dependencies, and also, configuring the environment appropriately. All one is required to do is simply push the docker image to the image repository, and deploy the container. Elastic Beanstalk service, then, takes care of different aspects of deployment such as capacity provisioning, load balancing, auto-scaling, application/service health monitoring etc.

Docker platform for Elastic Beanstalk has two generic configurations such as following: 
 - Single container Docker
 - Multicontainer Docker

We shall try and cover the use cases for both the configuration types.

## Single Container Docker

Before gettiing into details and related source code, lets quickly look into solution architecture for deploying microservices on AWS using AWS Elastic Beanstalk.

### Solution Architecture

Following represents the **solution architecture** of deploying microservices on AWS using AWS Elastic Beanstalk.

![Solution Architecture - Microservices to AWS Elastic Beanstalk](images/aws_eb.png)

In the above diagram, pay attention to some of the following:

 1. Code is checked into code repository such as Gitlab
 2. Webhook configured in GitLab triggers the Jenkins job
 3. Jenkins job starts executing which results in following steps:
    - Retrieve the microservice artifacts from Gitlab
    - Build the microservice
    - Run the tests such as unit and integration tests
    - Build the image if all of the above steps are successful
    - Push the image to image repository such as Dockerhub or AWS ECR
    - Deploy using AWS Elastic Beanstalk CLI command 

