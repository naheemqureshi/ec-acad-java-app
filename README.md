# **GL Engineering Centre - Academy**


## **Overview**
CI/CD example to build, push and deploy your application to AWS ECS containers using Jenkins Declarative Pipeline.


### **Tech Stack:**


#### **Github**
This contains our sample nodes application code.

#### **Jenkins**
JenkinsFile Containing the Jenkins declarative pipeline that is triggered on each pull request.

#### **Docker**
DockerFile containing image build information used by Dcoker on the Jenkins 'Building Image' stage to build the image containing the build artifacts.

#### **AWS ECR**
Used to store the Docker image received from Jenkins as part of the 'Push Image to ECR' stage.

#### **ECS**
Deploys the application as a container based on the image stored in ECR using the 'Deploy Image to ECR' stage.



### **Pipeline Stages:**

**Declarative Checkout SCM**: Checks out src from repo

**Declarative Tool Install**:Install build tools (Maven/Node) required as part of the 'Build & Unit Test' stage

**Build & Unit Test**: Builds src dependencies and performs unit tests

**Buidling Image**: Builds local Docker image in Jenkins workspace from build artifact

**Pushing Image to ECR**: Pushes built image to ECR

**Image Cleanup**: Removes all local images created by Docker locally

**Deploying Image**: Deploys image to ECS cluster service from ECR image
