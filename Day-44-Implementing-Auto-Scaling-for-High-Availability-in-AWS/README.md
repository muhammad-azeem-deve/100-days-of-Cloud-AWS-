# Day 44 - Implementing Auto Scaling for High Availability in AWS

## Challenge Overview

This is Day 44 of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The challenge was related to **AWS Auto Scaling, EC2 Launch Templates, Application Load Balancers, Target Groups, and High Availability**.

The Nautilus DevOps team wanted to build a highly available web application infrastructure on AWS. The goal was to ensure that the application could handle changing workloads by automatically adjusting the number of EC2 instances based on CPU utilization.

To achieve this, I configured an EC2 Launch Template, created an Auto Scaling Group, configured a target group, and deployed an Application Load Balancer to distribute incoming HTTP traffic across healthy EC2 instances.

The entire infrastructure was configured in the **us-east-1** AWS region.

### Challenge Requirement

The requirements were:

1. Create an EC2 Launch Template named `devops-launch-template`.
2. Use the Amazon Linux 2023 AMI.
3. Select `t2.micro` as the instance type.
4. Configure a security group allowing HTTP traffic on port 80.
5. Add User Data to install, start, and enable Nginx.
6. Create an Auto Scaling Group named `devops-asg`.
7. Configure the Auto Scaling Group with:
   - Minimum capacity: 1
   - Desired capacity: 1
   - Maximum capacity: 2
8. Configure a target tracking scaling policy based on average CPU utilization with a 50% target.
9. Create a target group named `devops-tg`.
10. Create an Application Load Balancer named `devops-alb`.
11. Configure the ALB to listen on HTTP port 80.
12. Associate the Auto Scaling Group with the target group and Application Load Balancer.
13. Configure health checks to ensure traffic is forwarded only to healthy instances.
14. Verify the ALB DNS name and confirm that the default Nginx welcome page is accessible.

---

## Objectives

The objectives of this challenge are:

- Understand AWS Auto Scaling and High Availability.
- Create and configure EC2 Launch Templates.
- Launch EC2 instances using Amazon Linux 2023.
- Configure EC2 User Data scripts.
- Automate Nginx installation using User Data.
- Create and configure Auto Scaling Groups.
- Understand minimum, desired, and maximum instance capacity.
- Configure target tracking scaling policies.
- Understand CPU-based automatic scaling.
- Create and configure Application Load Balancers.
- Configure target groups and health checks.
- Distribute HTTP traffic across multiple EC2 instances.
- Verify application availability through the ALB DNS name.

---

## Environment

| Item | Details |
| ------------------------- | -------------------------------- |
| Challenge | 100 Days of Cloud (AWS) |
| Platform | KodeKloud |
| Day | 44 |
| Cloud Provider | Amazon Web Services (AWS) |
| AWS Region | `us-east-1` |
| EC2 AMI | Amazon Linux 2023 |
| Instance Type | `t2.micro` |
| Launch Template | `devops-launch-template` |
| Security Group | HTTP - Port 80 |
| Web Server | Nginx |
| Auto Scaling Group | `devops-asg` |
| Minimum Capacity | 1 |
| Desired Capacity | 1 |
| Maximum Capacity | 2 |
| Scaling Policy | Target Tracking |
| CPU Utilization Target | 50% |
| Target Group | `devops-tg` |
| Load Balancer | `devops-alb` |
| Load Balancer Type | Application Load Balancer |
| Listener | HTTP - Port 80 |
| Status | Completed |

---

# Solution

## Step 1: Access the AWS Management Console

First, I accessed the AWS Management Console using the temporary credentials provided by the KodeKloud lab environment.

I selected the required AWS region:

```text
us-east-1
```

All resources for this challenge were created in the specified region.

---

## Step 2: Create the Security Group

Before creating the Launch Template, I configured a security group to allow HTTP traffic.

I opened:

```text
EC2
  -> Network & Security
  -> Security Groups
  -> Create Security Group
```

I configured the inbound rule:

| Type | Protocol | Port | Source |
| -------- | -------- | ---- | -------- |
| HTTP | TCP | 80 | 0.0.0.0/0 |

This allowed incoming HTTP requests from the internet.

The security group was attached to the EC2 instances launched through the Launch Template.

---

## Step 3: Create the EC2 Launch Template

I navigated to:

```text
EC2
  -> Instances
  -> Launch Templates
  -> Create Launch Template
```

I configured the Launch Template with the following settings:

```text
Launch Template Name:
devops-launch-template

AMI:
Amazon Linux 2023

Instance Type:
t2.micro

Security Group:
HTTP - Port 80
```

The Launch Template defines the configuration that AWS uses when launching EC2 instances.

---

## Step 4: Configure User Data

I added a User Data script to automatically install and configure Nginx whenever an EC2 instance launches.

The script was:

```bash
#!/bin/bash

dnf update -y

dnf install nginx -y

systemctl start nginx

systemctl enable nginx
```

### Explanation

- `dnf update -y`: Updates available system packages.
- `dnf install nginx -y`: Installs the Nginx web server.
- `systemctl start nginx`: Starts the Nginx service.
- `systemctl enable nginx`: Enables Nginx to start automatically during system boot.

This automation ensures that every EC2 instance launched by the Auto Scaling Group has Nginx installed and running.

After configuring User Data, I created the Launch Template.

---

## Step 5: Create the Auto Scaling Group

Next, I navigated to:

```text
EC2
  -> Auto Scaling Groups
  -> Create Auto Scaling Group
```

I entered the following configuration:

```text
Auto Scaling Group Name:
devops-asg

Launch Template:
devops-launch-template
```

I selected the Launch Template created in the previous step.

I then configured the desired network settings and selected the required VPC and subnets.

---

## Step 6: Configure Auto Scaling Capacity

I configured the Auto Scaling Group capacity:

```text
Minimum Capacity: 1

Desired Capacity: 1

Maximum Capacity: 2
```

### Explanation

- **Minimum Capacity:** The Auto Scaling Group maintains at least one EC2 instance.
- **Desired Capacity:** AWS initially attempts to maintain one running EC2 instance.
- **Maximum Capacity:** The group can scale out to a maximum of two EC2 instances.

This configuration allows the application to increase its capacity when workload demand increases.

---

## Step 7: Configure Target Tracking Scaling Policy

I configured a target tracking scaling policy based on average CPU utilization.

The policy settings were:

```text
Policy Type:
Target Tracking Scaling

Metric:
Average CPU Utilization

Target Value:
50%
```

The Auto Scaling Group monitors CPU utilization and adjusts the number of EC2 instances to maintain the configured target.

For example:

- When CPU utilization increases, AWS can launch another EC2 instance.
- When CPU utilization decreases, AWS can terminate an instance if the desired capacity can be reduced without going below the minimum.

The maximum number of instances remained limited to two.

---

## Step 8: Create the Target Group

Next, I created a target group to register EC2 instances and allow the Application Load Balancer to forward incoming traffic to them.

I navigated to:

```text
EC2
  -> Load Balancing
  -> Target Groups
  -> Create Target Group
```

I configured the target group:

```text
Target Group Name:
devops-tg

Target Type:
Instances

Protocol:
HTTP

Port:
80
```

I configured the health check settings:

```text
Health Check Protocol:
HTTP

Health Check Path:
/ 
```

The target group was created successfully.

The target group is responsible for checking the health of registered EC2 instances and reporting their status to the load balancer.

---

## Step 9: Create the Application Load Balancer

I navigated to:

```text
EC2
  -> Load Balancing
  -> Load Balancers
  -> Create Load Balancer
```

I selected:

```text
Application Load Balancer
```

I configured the load balancer:

```text
Load Balancer Name:
devops-alb

Scheme:
Internet-facing

IP Address Type:
IPv4
```

I selected the required VPC and subnets in the appropriate Availability Zones.

---

## Step 10: Configure the ALB Security Group and Listener

I configured the Application Load Balancer security group to allow incoming HTTP traffic.

The inbound rule was:

| Type | Protocol | Port | Source |
| -------- | -------- | ---- | -------- |
| HTTP | TCP | 80 | 0.0.0.0/0 |

I also configured the listener:

```text
Listener Protocol:
HTTP

Listener Port:
80

Default Action:
Forward to devops-tg
```

This configuration allows the ALB to receive HTTP requests and forward them to the target group.

**Note:** The EC2 instance security group must also allow HTTP traffic from the ALB security group so that the load balancer can communicate with the backend instances.

---

## Step 11: Associate the Target Group with the Auto Scaling Group

After creating the Application Load Balancer and target group, I associated the target group with the Auto Scaling Group.

I opened:

```text
EC2
  -> Auto Scaling Groups
  -> devops-asg
  -> Edit
```

I selected the target group:

```text
devops-tg
```

This integration allows newly launched EC2 instances to be registered automatically with the target group.

Similarly, instances removed by Auto Scaling are deregistered.

---

## Step 12: Verify Auto Scaling Group Instances

After completing the configuration, I checked the Auto Scaling Group.

The initial configuration was:

```text
Auto Scaling Group:
devops-asg

Minimum:
1

Desired:
1

Maximum:
2
```

I verified that the EC2 instance launched successfully using the configured Launch Template.

I also checked that the Nginx service was running on the instance.

---

## Step 13: Verify Target Group Health

I navigated to:

```text
EC2
  -> Target Groups
  -> devops-tg
  -> Targets
```

I checked the registered EC2 instances.

The expected target health status was:

```text
Healthy
```

A healthy target indicates that the instance is responding successfully to the configured health checks.

The ALB forwards traffic only to healthy registered targets.

---

## Step 14: Verify the Application Load Balancer

After the Application Load Balancer became active, I opened:

```text
EC2
  -> Load Balancers
  -> devops-alb
```

I copied the DNS name provided by AWS.

The DNS name had a format similar to:

```text
devops-alb-xxxxxxxx.us-east-1.elb.amazonaws.com
```

I opened the DNS name in a web browser using HTTP:

```text
http://<ALB-DNS-NAME>
```

The browser displayed the default Nginx welcome page.

This confirmed that:

- The Application Load Balancer was reachable.
- The HTTP listener was working.
- The target group was forwarding requests.
- The EC2 instance was responding to health checks.
- Nginx was successfully installed and running.

---

# AWS Architecture Workflow

The overall architecture for this challenge was:

```text
                Internet
                    |
                    |
                    v
          Application Load Balancer
                 devops-alb
                    |
              HTTP : 80
                    |
                    v
              Target Group
                devops-tg
                    |
             Health Checks
                    |
          +---------+---------+
          |                   |
          v                   v
       EC2 Instance        EC2 Instance
       t2.micro            t2.micro
       Nginx               Nginx
          ^                   ^
          |                   |
          +---------+---------+
                    |
                    |
             Auto Scaling Group
                 devops-asg
                    |
              CPU Target: 50%
                    |
              Min: 1
              Desired: 1
              Max: 2
                    |
                    v
            Launch Template
       devops-launch-template
                    |
             Amazon Linux 2023
```

---

# What I Learned

From this challenge, I learned and practiced:

- How to create EC2 Launch Templates.
- How to use Amazon Linux 2023 AMIs.
- How to configure EC2 security groups.
- How to automate software installation using User Data.
- How to install and configure Nginx automatically.
- How to create Auto Scaling Groups.
- How minimum, desired, and maximum capacity work.
- How to configure target tracking scaling policies.
- How CPU utilization can trigger automatic scaling.
- How to create Application Load Balancers.
- How to configure target groups and HTTP listeners.
- How to associate Auto Scaling Groups with target groups.
- How ALB health checks work.
- How to distribute incoming traffic across EC2 instances.
- How to verify application availability through an ALB DNS name.
- How AWS services work together to provide high availability.

# Challenge Status

Day 44 — Completed Successfully ✅

**Created the** **`devops-launch-template`** **using Amazon Linux 2023, configured the** **`devops-asg`** **with target tracking based on 50% CPU utilization, created the** **`devops-tg`** **target group, and deployed the** **`devops-alb`** **Application Load Balancer. Verified that the ALB DNS name successfully served the default Nginx welcome page.**

100 Days. 100 Challenges. One Cloud Journey. ☁️🚀

I will continue documenting each challenge and building my practical AWS Cloud and DevOps skills step by step.
