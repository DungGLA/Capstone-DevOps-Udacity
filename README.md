# Udacity Cloud DevOps Capstone Project - UP

This is final project of Udacity Cloud DevOps Engineer Nanodegree Program.

## Project Overview

In this project I have implemented all the knowledge that I have learnt from the Udacity Cloud DevOps Engineer Nanodegree program. In this project I have

1. Created Jenkins server using the AWS CloudFormation.
2. Installed all the needed tools in Jenkins master server using the Launch Configuration.
3. Creates EKS cluster in AWS.
   using the bellow command, it will indirectly create EKS cluster using CloudFormation

```sh
eksctl create cluster --name udacitycluster --nodegroup-name standard-workers --node-type t2.micro --nodes 2 --nodes-min 1 --nodes-max 3 --region us-east-1 --zones us-east-1a --zones us-east-1b --zones us-east-1c
```

4. Created application deployment pipeline that deploys the application docker container in the kubernetes cluster.
5. Used blue/green deployment strategy to deploy the application.

## Project Files

#### infrastructure

This folder all the files related to infrastructure deployment.

- `jenkins-server-parameters.json`: Parameters file for cloud formation stack.
- `jenkins-server.yml`: CloudFormation template for creating jenkins server.

Script to create, delete and update CloudFormation stack.

- `create-jenkins.sh`
- `update-jenkins.sh`
- `delete-jenkins.sh`

#### kubernetes-resources

This folder contains all template files for Kubernetes resources.

- `blue-replication-controller.yml`: A replication controller template that creates pods with label as `app=blue`.
- `blue-service.yml`: A service template that selects all the pods with label as `app=blue`.
- `green-replication-controller.yml`: A replication controller template that creates pods with label as `app=green`.
- `green-service.yml`: A service template that selects all the pods with label as `app=green`.

#### screenshots

This folder contains all screenshots taken during creation of this project.

#### docker

This is Dockerfile of application.

- `blue/Dockerfile`: Application with blue background.
- `green/Dockerfile`: Application with green background.

#### Jenkinsfile

This file contains the steps of CICD pipeline of application.

## Project Setup

- Create Jenkins server using cloud formation template.

```
$ ./create-jenkins.sh jenkins-stack jenkins-server.yml jenkins-server-parameters.json
```

- Following resources are created after executing above command:
  - VPC
  - Subnet
  - Route Table
  - Route
  - Internet Gateway
  - Security Group
  - Launch Configuration
  - Auto Scaling Group
- Following tools are automatically installed in the server using Launch Configuration:
  - Jenkins
  - Docker
  - AWS CLI
  - eksctl CLI
  - kubectl CLI
  - Tidy
- Once the stack creation is complete, access the Jenkins UI using `http://<EC2_PUBLIC_IP>:8080`
- Login using admin account and complete the initial setup of Jenkins.
- Install the following plugins in Jenkins:
  - [CloudBees AWS Credentials](https://plugins.jenkins.io/aws-credentials/)
  - [Blue Ocean](https://plugins.jenkins.io/blueocean/)
- Add AWS credentials in Jenkins.

- Creates a EKS cluster by SSH to the Jenkins EC2, then run the bellow command to create the EKS cluster

```sh
eksctl create cluster --name udacitycluster --nodegroup-name standard-workers --node-type t2.micro --nodes 2 --nodes-min 1 --nodes-max 3 --region us-east-1 --zones us-east-1a --zones us-east-1b --zones us-east-1c
```

- Configures kubectl so that we can connect to EKS cluster
  _Note: This pipeline will take around 15-20 minutes to complete._

- Add Docker Hub credentials in Jenkins so that we can push docker image to Docker Hub.
- Create new item in Jenkins of type `Pipeline`
- In the configuration page of provide the GitHub repository as `https://github.com/DungGLA/Capstone-DevOps-Udacity.git` and script path as `Jenkinsfile`
- Apply and save the pipeline.
- Click on `Build Now` to trigger the pipeline
- Once the pipeline passes with stage name `Create Service Pointing to Blue Replication Controller`, go to Load Balancer page in AWS console and look for DNS name
- DNS name will be something like `http://af0dfc67d36cf4f469afd28a760eb1ef-1149866563.us-east-1.elb.amazonaws.com`
- In the browser, open a new tab and hit link as `http://<DNS_NAME>:8000/`. It will show the capstone project website.
- Approve the pipeline to proceed to next stage
- Once the pipeline is complete, service will be pointing to pods that has label as `app=green`
