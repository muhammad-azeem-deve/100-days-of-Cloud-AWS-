# Day 38 - Deploying Containerized Applications with Amazon ECS

## Challenge Overview

This is Day 38 of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The challenge was related to **Amazon Elastic Container Registry (ECR), Amazon Elastic Container Service (ECS), Docker, and AWS Fargate**.

The Nautilus DevOps team wanted to deploy a containerized application using AWS container services. The task involved creating a private Amazon ECR repository to store Docker images and deploying the application through Amazon ECS.

The application Dockerfile was already available on the `aws-client` host at:

```text
/root/pyapp
```

The Docker image needed to be built, tagged as `latest`, and pushed to the private ECR repository. After that, an ECS cluster, task definition, and service had to be configured to run the application using Fargate.

### Challenge Requirement

The requirements were:

1. Create a private ECR repository named `datacenter-ecr`.
2. Use the Dockerfile located at `/root/pyapp` on the `aws-client` host.
3. Build a Docker image from the provided Dockerfile.
4. Tag the Docker image as `latest`.
5. Push the Docker image to the `datacenter-ecr` repository.
6. Create an ECS cluster named `datacenter-cluster` using Fargate.
7. Create an ECS task definition named `datacenter-taskdefinition`.
8. Configure the task definition to use the image stored in ECR.
9. Create an ECS service named `datacenter-service`.
10. Ensure that at least one task is running successfully.

---

## Objectives

The objectives of this challenge are:

- Understand containerization in AWS.
- Learn how Amazon ECR stores Docker images.
- Create a private ECR repository.
- Build and push Docker images to Amazon ECR.
- Understand Amazon ECS and its components.
- Create an ECS cluster using Fargate.
- Configure an ECS task definition.
- Deploy a containerized application using an ECS service.
- Configure networking and security groups for application access.
- Verify the running application through the AWS Console and AWS CLI.

---

## Environment

| Item | Details |
|---|---|
| Challenge | 100 Days of Cloud (AWS) |
| Platform | KodeKloud |
| Day | 38 |
| Cloud Provider | Amazon Web Services |
| Region | `us-east-1` |
| Container Registry | Amazon ECR |
| ECR Repository | `datacenter-ecr` |
| Image Tag | `latest` |
| Dockerfile Location | `/root/pyapp` |
| Container Service | Amazon ECS |
| Launch Type | AWS Fargate |
| ECS Cluster | `datacenter-cluster` |
| Task Definition | `datacenter-taskdefinition` |
| ECS Service | `datacenter-service` |
| Container Port | `80` |
| Desired Tasks | `1` |
| Status | Completed |

---

# Solution

## Step 1: Access the AWS Management Console

First, I accessed the AWS Management Console using the temporary credentials provided by the KodeKloud lab environment.

I selected the required AWS region:

```text
us-east-1
```

All AWS resources for this challenge were created in this region.

---

## Step 2: Create a Private ECR Repository

Amazon Elastic Container Registry (ECR) is a fully managed container image registry that allows us to store and manage Docker images.

I opened the Amazon ECR service from the AWS Management Console.

Navigation:

```text
AWS Management Console
        |
        v
Amazon ECR
        |
        v
Private Registry
        |
        v
Repositories
        |
        v
Create Repository
```

I selected **Create repository** and configured the following settings:

```text
Visibility:       Private
Repository Name:  datacenter-ecr
```

I kept the remaining settings at their default values and created the repository.

After creation, I opened the repository and copied its URI.

The repository URI follows this format:

```text
<ACCOUNT_ID>.dkr.ecr.us-east-1.amazonaws.com/datacenter-ecr
```

The actual account ID is provided by the KodeKloud AWS lab.

---

## Step 3: Access the AWS Client Machine

After creating the ECR repository, I accessed the `aws-client` machine.

The Dockerfile was already available at:

```text
/root/pyapp
```

I navigated to the application directory:

```bash
cd /root/pyapp
```

I checked the available files:

```bash
ls -la
```

I verified that the Dockerfile was present before building the image.

---

## Step 4: Build the Docker Image

I used the Dockerfile provided by the KodeKloud lab to build the application image.

The image was tagged with the ECR repository URI and the `latest` tag.

Command:

```bash
docker build -t <ACCOUNT_ID>.dkr.ecr.us-east-1.amazonaws.com/datacenter-ecr:latest .
```

This command built the Docker image using the Dockerfile in the current directory.

---

## Step 5: Authenticate Docker with Amazon ECR

Before pushing the Docker image, I authenticated Docker with the private ECR repository.

I used the AWS CLI authentication command:

```bash
aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin <ACCOUNT_ID>.dkr.ecr.us-east-1.amazonaws.com
```

After successful authentication, Docker was able to communicate with the private ECR registry.

Expected message:

```text
Login Succeeded
```

---

## Step 6: Push the Docker Image to ECR

After authentication, I pushed the image to the private repository:

```bash
docker push <ACCOUNT_ID>.dkr.ecr.us-east-1.amazonaws.com/datacenter-ecr:latest
```

The image was uploaded to Amazon ECR.

I returned to the AWS Management Console and opened:

```text
Amazon ECR
    |
    v
Repositories
    |
    v
datacenter-ecr
    |
    v
Images
```

I verified that the image appeared with the `latest` tag.

---

## Step 7: Create an ECS Cluster

After successfully uploading the Docker image, I opened the Amazon ECS service.

Navigation:

```text
AWS Management Console
        |
        v
Amazon ECS
        |
        v
Clusters
        |
        v
Create Cluster
```

I configured the cluster:

```text
Cluster Name: datacenter-cluster
Infrastructure: AWS Fargate
```

I kept the remaining settings at their default values and selected **Create**.

After creation, the ECS cluster appeared in the Clusters section.

---

## Step 8: Create an ECS Task Definition

An ECS task definition is a blueprint that specifies how a container should run.

It defines the container image, CPU, memory, port mappings, and other runtime settings.

I navigated to:

```text
Amazon ECS
    |
    v
Task Definitions
    |
    v
Create New Task Definition
```

I selected **AWS Fargate** as the launch type.

### Task Definition Configuration

I configured the following settings:

```text
Task Definition Family: datacenter-taskdefinition

Launch Type: Fargate

Operating System: Linux

CPU: 0.25 vCPU (256 units)

Memory: 512 MiB

Task Role: None
```

### Container Configuration

I added the application container with the following configuration:

```text
Container Name: datacenter-container

Image URI:
<ACCOUNT_ID>.dkr.ecr.us-east-1.amazonaws.com/datacenter-ecr:latest

Container Port: 80

Protocol: TCP
```

I selected **Create** to register the task definition.

The task definition was now available for deploying the application.

---

## Step 9: Create an ECS Service

An ECS service manages the desired number of running tasks and helps maintain application availability.

I opened the previously created cluster:

```text
Amazon ECS
    |
    v
Clusters
    |
    v
datacenter-cluster
    |
    v
Create Service
```

### Service Configuration

I configured the service using the following settings:

```text
Compute Options: Launch Type

Launch Type: Fargate

Task Definition: datacenter-taskdefinition

Service Name: datacenter-service

Desired Tasks: 1
```

### Networking Configuration

I configured the networking settings:

```text
VPC: Default VPC

Subnets: Select available subnets

Security Group: Allow HTTP traffic

Inbound Rule:
Type: HTTP
Protocol: TCP
Port: 80
Source: 0.0.0.0/0

Auto-assign Public IP: Enabled
```

The security group must allow inbound HTTP traffic on the application's container port.

I selected **Create Service** to deploy the application.

---

## Step 10: Verify the ECS Service

After creating the service, I opened:

```text
Amazon ECS
    |
    v
Clusters
    |
    v
datacenter-cluster
    |
    v
Services
    |
    v
datacenter-service
```

I checked the service status and deployment information.

The important configurations to verify were:

```text
Service Name: datacenter-service

Service Status: ACTIVE

Desired Tasks: 1

Running Tasks: 1

Launch Type: Fargate
```

---

## Step 11: Verify the Running ECS Task

I opened the service and navigated to the **Tasks** tab.

I verified that the application task was running.

The expected configuration was:

```text
Task Definition: datacenter-taskdefinition

Last Status: RUNNING

Desired Status: RUNNING
```

I opened the running task to inspect its details.

I also checked the networking section to find the assigned public IP address.

---

## Step 12: Access the Deployed Application

To verify that the application was accessible, I copied the public IP address assigned to the running Fargate task.

I opened a browser and accessed:

```text
http://<PUBLIC_IP>
```

The expected application response was:

```text
Welcome to KKE AWS cloud labs!
```

This confirmed that the containerized application was deployed and accessible.

**Important:** For successful access, the task's security group must allow inbound traffic on port 80, and the application must listen on the configured container port.

---

# AWS Console Verification

I performed the following checks through the AWS Management Console:

| Resource | Verification | Expected Result |
|---|---|---|
| ECR Repository | `datacenter-ecr` | Available |
| ECR Image | `latest` | Image uploaded |
| ECS Cluster | `datacenter-cluster` | Active |
| Task Definition | `datacenter-taskdefinition` | Registered |
| ECS Service | `datacenter-service` | Active |
| Desired Tasks | 1 | 1 |
| Running Tasks | 1 | 1 |
| ECS Task | Application task | RUNNING |
| Application | Public IP and HTTP | Accessible |

---

# AWS ECS Deployment Workflow

The overall workflow for this challenge was:

```text
       Dockerfile
      /root/pyapp
           |
           v
     Build Docker Image
           |
           v
      Amazon ECR
    datacenter-ecr
           |
           v
     Push Image (latest)
           |
           v
      Amazon ECS
           |
           v
    datacenter-cluster
           |
           v
  datacenter-taskdefinition
           |
           v
    datacenter-service
           |
           v
      AWS Fargate
           |
           v
   Running Application
           |
           v
     Access via HTTP
```

---

# What I Learned

From this challenge, I learned and practiced:

- How to create a private Amazon ECR repository.
- How to build Docker images using a Dockerfile.
- How to authenticate Docker with Amazon ECR.
- How to push Docker images to a private ECR repository.
- How Amazon ECS manages containerized applications.
- How to create an ECS cluster using AWS Fargate.
- How to configure an ECS task definition.
- How to configure container images and port mappings.
- How to create an ECS service.
- How to configure Fargate networking and public IP access.
- How to configure security groups for HTTP traffic.
- How to monitor running ECS tasks.
- How to verify a deployed application through its public IP address.

# Challenge Status

Day 38 — Completed Successfully ✅

**Created the private** **`datacenter-ecr`** **repository, built and pushed the Docker image, configured the** **`datacenter-cluster`** **using Fargate, registered the** **`datacenter-taskdefinition`** **task definition, and deployed the application through the** **`datacenter-service`** **ECS service.**

100 Days. 100 Challenges. One Cloud Journey. ☁️🚀

I will continue documenting each challenge and building my practical AWS cloud skills step by step.
