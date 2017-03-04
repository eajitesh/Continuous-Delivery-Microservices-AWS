# Deep Dive into Continuous Delivery of Microservices on AWS

The goal of this project is to enable developers play with continuous delivery of microservices on cloud platforms such as AWS. Those planning to do **startups** could get tips and code aamples to get started with development, testing and releasing (continuously deploying) the software thereby gaining the competitive edge for their products.


The project would demonstrate aome of the following:

1. Developers check-in the code into the code repository such as GitLab. 
2. GitLab webhook triggers the Jenkins job 
3. Jenkins job starts with getting code from repository, build the code, run the unit tests/integration 
4. Once build and tests run successfully, Jenkins build the image for Microservices and push the same to either of Dockerhub or AWS ECR.
5. After the docker image for microservices is pushed to the image repository, following is done to deploy microservices (woukd run within containers) on AWS ECS Cluster or AWS Elastic Beanstalk (EB).

- **AWS ECS Cluster**: For deploying to ECS cluster, a new task definition is registered and the ECS service is updated. As a result of updating ECS service, the container using microservices image (pulled from image repository) gets started.
- **AWS Elastic Beanstalk**: For deploying the microservices on AWS EB, the execution of EB Cli command related with deployment is executed which leads to starting of container (with microservice image) on EC2 instance on AWS EB environment.


This project is intended to provide information related with some of the following topics.
```markdown
1. Setup Gitlab and Jenkins (relevant plugins)
2. Setup Dockerhub, AWS ECR
3. Setup AWS ECS 
4. Setup AWS Elastic Beanstalk 
5. Configure Jenkins post-steps for deployment to AWS ECS
6. Confifure Jenkins post-steps for deployment to AWS Elastic Beanstalk
```
## Setup Gitlab and Jenkins


The first step is to get setup with the development envitonment. We shall use Docker images for GitLab and Jenkins and, Docker-compose for starting/commissioning the environment. Once environment is commissioned, it would be required to login into both GitLab and Jenkins to do some of the following:

1. **GitLab**: Ceate code repository

2. **Jenkins**:Install plugins,configure system, create job, configure job  etc.  

Read greater details on this page, [Jenkins Gitlab with Docker Containers](https://github.com/eajitesh/Continuous-Delivery-Microservices-AWS/blob/master/jenkins-gitlab-setup.md)

## Get setup with Dockerhub and AWS account    

Next step is to create your account with image repository such as Dockerhub and AWS ECR. 

1. **Dockerhub**: Go to [Dockerhub](http://www.dockerhub.com) and get signed up. You will be using this credentials from within Jenkins to login to Dockerhub and push the images.
2. **AWS**: Go to [AWS](http://www.aws.com) and get signed up. 

## Get Setup with AWS ECS

As part of getting setup with ECS, following needs to be done:

1. Create a repository (EC2 Repository - ECR)
2. Create a task definition 
3. Create a service
4. Create an ECS cluster

Read greater details on this page, [Set up AWS ECS](http://)


[edit](https://github.com/eajitesh/cloud-native-aws-demo/edit/master/README.md)
