# Day 38 - AWS ECS Verification Commands

This file contains only the **AWS CLI verification commands** used for Day 38 of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

All AWS resources were created using the AWS Management Console. These commands are used to verify the ECR repository, Docker image, ECS cluster, task definition, ECS service, and running application.

**AWS Region:** `us-east-1`

---

# 1. Verify AWS CLI Configuration

Check the AWS CLI version:

```bash
aws --version
```

Verify the active AWS identity:

```bash
aws sts get-caller-identity
```

---

# 2. Verify ECR Repository

List all private ECR repositories:

```bash
aws ecr describe-repositories \
    --region us-east-1 \
    --query 'repositories[*].repositoryName' \
    --output table
```

Verify the specific repository:

```bash
aws ecr describe-repositories \
    --repository-names datacenter-ecr \
    --region us-east-1
```

---

# 3. Verify Docker Image in ECR

List images stored in the repository:

```bash
aws ecr describe-images \
    --repository-name datacenter-ecr \
    --region us-east-1
```

Verify the latest image tag:

```bash
aws ecr describe-images \
    --repository-name datacenter-ecr \
    --region us-east-1 \
    --query 'imageDetails[*].[imageTags,imageDigest,imagePushedAt]' \
    --output table
```

Expected information:

```text
Repository: datacenter-ecr
Image Tag: latest
```

---

# 4. Verify ECS Cluster

List all ECS clusters:

```bash
aws ecs list-clusters \
    --region us-east-1
```

Describe the specific ECS cluster:

```bash
aws ecs describe-clusters \
    --clusters datacenter-cluster \
    --region us-east-1
```

Display cluster status:

```bash
aws ecs describe-clusters \
    --clusters datacenter-cluster \
    --region us-east-1 \
    --query 'clusters[*].[clusterName,status,runningTasksCount,activeServicesCount]' \
    --output table
```

Expected information:

```text
Cluster Name: datacenter-cluster
Status: ACTIVE
```

---

# 5. Verify ECS Task Definition

List registered task definitions:

```bash
aws ecs list-task-definitions \
    --region us-east-1
```

Verify the specific task definition:

```bash
aws ecs describe-task-definition \
    --task-definition datacenter-taskdefinition \
    --region us-east-1
```

Display task definition configuration:

```bash
aws ecs describe-task-definition \
    --task-definition datacenter-taskdefinition \
    --region us-east-1 \
    --query 'taskDefinition.[family,cpu,memory,requiresCompatibilities]' \
    --output table
```

Verify the container image and port mapping:

```bash
aws ecs describe-task-definition \
    --task-definition datacenter-taskdefinition \
    --region us-east-1 \
    --query 'taskDefinition.containerDefinitions[*].[name,image,portMappings[*].containerPort]' \
    --output json
```

Expected configuration:

```text
Task Definition: datacenter-taskdefinition
CPU: 256
Memory: 512
Launch Type: FARGATE
Container Port: 80
```

---

# 6. Verify ECS Service

List services inside the cluster:

```bash
aws ecs list-services \
    --cluster datacenter-cluster \
    --region us-east-1
```

Describe the specific ECS service:

```bash
aws ecs describe-services \
    --cluster datacenter-cluster \
    --services datacenter-service \
    --region us-east-1
```

Display service status and task counts:

```bash
aws ecs describe-services \
    --cluster datacenter-cluster \
    --services datacenter-service \
    --region us-east-1 \
    --query 'services[*].[serviceName,status,desiredCount,runningCount,pendingCount]' \
    --output table
```

Expected configuration:

```text
Service Name: datacenter-service
Status: ACTIVE
Desired Tasks: 1
Running Tasks: 1
Pending Tasks: 0
```

---

# 7. Verify Running ECS Tasks

List the tasks running in the cluster:

```bash
aws ecs list-tasks \
    --cluster datacenter-cluster \
    --region us-east-1
```

List tasks associated with the service:

```bash
aws ecs list-tasks \
    --cluster datacenter-cluster \
    --service-name datacenter-service \
    --region us-east-1
```

Describe the running tasks:

```bash
aws ecs describe-tasks \
    --cluster datacenter-cluster \
    --tasks $(aws ecs list-tasks \
        --cluster datacenter-cluster \
        --service-name datacenter-service \
        --region us-east-1 \
        --query 'taskArns' \
        --output text) \
    --region us-east-1
```

Display task status:

```bash
aws ecs describe-tasks \
    --cluster datacenter-cluster \
    --tasks $(aws ecs list-tasks \
        --cluster datacenter-cluster \
        --service-name datacenter-service \
        --region us-east-1 \
        --query 'taskArns' \
        --output text) \
    --query 'tasks[*].[lastStatus,desiredStatus,launchType,taskDefinitionArn]' \
    --region us-east-1 \
    --output table
```

Expected information:

```text
Last Status: RUNNING
Desired Status: RUNNING
Launch Type: FARGATE
```

---

# 8. Verify Container Details

Display container information for the running task:

```bash
aws ecs describe-tasks \
    --cluster datacenter-cluster \
    --tasks $(aws ecs list-tasks \
        --cluster datacenter-cluster \
        --service-name datacenter-service \
        --region us-east-1 \
        --query 'taskArns' \
        --output text) \
    --region us-east-1 \
    --query 'tasks[*].containers[*].[name,image,lastStatus]' \
    --output table
```

Expected information:

```text
Container Name: datacenter-container
Image: datacenter-ecr:latest
Status: RUNNING
```

---

# 9. Verify Public IP Address

First, retrieve the network interface ID assigned to the running Fargate task:

```bash
aws ecs describe-tasks \
    --cluster datacenter-cluster \
    --tasks $(aws ecs list-tasks \
        --cluster datacenter-cluster \
        --service-name datacenter-service \
        --region us-east-1 \
        --query 'taskArns' \
        --output text) \
    --region us-east-1 \
    --query 'tasks[0].attachments[0].details[?name==`networkInterfaceId`].value' \
    --output text
```

Use the returned network interface ID to retrieve its public IP:

```bash
aws ec2 describe-network-interfaces \
    --network-interface-ids <NETWORK_INTERFACE_ID> \
    --region us-east-1 \
    --query 'NetworkInterfaces[0].Association.PublicIp' \
    --output text
```

---

# 10. Verify Application Accessibility

After retrieving the public IP address, test the application using:

```bash
curl http://<PUBLIC_IP>
```

Expected application response:

```text
Welcome to KKE AWS cloud labs!
```

This verifies that the deployed application is responding to HTTP requests.

---

# 11. Final Verification Summary

| Resource | Name | Expected Status |
|---|---|---|
| ECR Repository | datacenter-ecr | Available |
| Docker Image | latest | Uploaded |
| ECS Cluster | datacenter-cluster | ACTIVE |
| Task Definition | datacenter-taskdefinition | Registered |
| ECS Service | datacenter-service | ACTIVE |
| ECS Task | Fargate Task | RUNNING |
| Running Tasks | 1 | 1 |
| Application | HTTP Port 80 | Accessible |

---

# Day 38 Result

```text
ECR Repository       : datacenter-ecr
Docker Image         : latest
ECS Cluster          : datacenter-cluster
Task Definition      : datacenter-taskdefinition
ECS Service          : datacenter-service
Launch Type          : FARGATE
Desired Tasks        : 1
Running Tasks        : 1
Application Port     : 80
Application Status   : Accessible
```

**Day 38 — Completed Successfully ✅**

100 Days. 100 Challenges. One Cloud Journey. ☁️🚀
