# Day 36 - Load Balancing EC2 Instances with Application Load Balancer

## Challenge Overview

This is Day 36 of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The challenge was related to **Amazon EC2, Application Load Balancer (ALB), Target Groups, Security Groups, and Web Server Configuration**.

The Nautilus Development Team needed to set up an EC2 instance and configure it to run a web server. The EC2 instance had to be integrated with an Application Load Balancer to provide a reliable entry point for incoming web traffic.

The objective was to create an EC2 instance running Nginx, configure a target group, and set up an Application Load Balancer to forward HTTP requests to the EC2 instance.

### Challenge Requirement

The requirements were:

1. Create a security group named `xfusion-sg`.
2. Configure the security group to allow HTTP traffic on port `80` from the ALB's default security group.
3. Create an EC2 instance named `xfusion-ec2`.
4. Use any available Ubuntu AMI.
5. Configure User Data to install and start the Nginx web server.
6. Create a target group named `xfusion-tg`.
7. Configure the target group to use HTTP on port `80`.
8. Register the `xfusion-ec2` instance with the target group.
9. Create an Application Load Balancer named `xfusion-alb`.
10. Attach the default security group to the ALB and allow public HTTP traffic on port `80`.
11. Configure the ALB listener to forward requests to `xfusion-tg`.
12. Verify that the Nginx web server is accessible through the ALB DNS name.

---

## Objectives

The objectives of this challenge are:

- Understand the purpose of an Application Load Balancer.
- Learn how to configure EC2 instances as web servers.
- Create and configure AWS security groups.
- Install and configure Nginx using EC2 User Data.
- Create and configure target groups.
- Register EC2 instances with target groups.
- Configure an Application Load Balancer.
- Understand listeners and forwarding rules.
- Configure HTTP health checks.
- Verify target health and load balancer connectivity.
- Understand how load balancing improves application availability.

---

## Environment

| Item | Details |
| ----------------- | ------------------------------ |
| Challenge | 100 Days of Cloud (AWS) |
| Platform | KodeKloud |
| Day | 36 |
| Cloud Provider | Amazon Web Services (AWS) |
| AWS Region | `us-east-1` |
| EC2 Instance | `xfusion-ec2` |
| Operating System | Ubuntu |
| Web Server | Nginx |
| EC2 Security Group | `xfusion-sg` |
| Target Group | `xfusion-tg` |
| Load Balancer | `xfusion-alb` |
| Listener Protocol | HTTP |
| Listener Port | 80 |
| Target Protocol | HTTP |
| Target Port | 80 |
| Status | Completed |

---

# Solution

The complete implementation was performed using the **AWS Management Console**. AWS CLI commands were used only for verification.

## Step 1: Access the AWS Management Console

First, I logged into the AWS Management Console using the temporary credentials provided by the KodeKloud lab environment.

I selected the required AWS region:

```text
us-east-1
```

All resources for this challenge were created in this region.

---

## Step 2: Create Security Group xfusion-sg

First, I created a security group for the EC2 instance.

I navigated to:

```text
AWS Console
    |
    v
EC2
    |
    v
Network & Security
    |
    v
Security Groups
    |
    v
Create Security Group
```

I configured the security group with the following details:

```text
Security Group Name: xfusion-sg
Description: Allow HTTP traffic from ALB
VPC: Default VPC
```

### Configure Inbound Rules

I added the following inbound rule:

| Type | Protocol | Port | Source |
|---|---|---|---|
| HTTP | TCP | 80 | Default ALB Security Group |

The source was configured using the security group ID of the default security group that would be attached to the Application Load Balancer.

This allows the EC2 instance to receive HTTP traffic from the ALB rather than directly from the public internet.

I kept the default outbound rules.

Finally, I clicked **Create security group**.

---

## Step 3: Launch EC2 Instance xfusion-ec2

After creating the security group, I launched a new EC2 instance.

I navigated to:

```text
EC2
  |
  v
Instances
  |
  v
Launch Instances
```

I configured the instance with the following settings:

### Basic Configuration

```text
Name: xfusion-ec2
AMI: Ubuntu
Instance Type: t2.micro
Key Pair: Existing or newly created key pair
```

### Network Settings

I configured the networking as follows:

```text
VPC: Default VPC
Subnet: Public Subnet
Auto-assign Public IP: Enable
Security Group: xfusion-sg
```

### Configure User Data

To automatically install and start Nginx when the instance launches, I opened:

```text
Advanced Details
    |
    v
User Data
```

I entered the following script:

```bash
#!/bin/bash

apt update -y
apt install nginx -y

systemctl start nginx
systemctl enable nginx
```

This script performs the following operations:

- Updates the Ubuntu package repository.
- Installs the Nginx web server.
- Starts the Nginx service.
- Enables Nginx to start automatically after a reboot.

After completing the configuration, I clicked **Launch Instance**.

I waited until the instance reached the following state:

```text
Instance State: Running
Status Checks: 2/2 checks passed
```

---

## Step 4: Create Target Group xfusion-tg

After launching the EC2 instance, I created a target group.

I navigated to:

```text
EC2
  |
  v
Load Balancing
  |
  v
Target Groups
  |
  v
Create Target Group
```

I configured the target group with the following settings:

### Basic Configuration

```text
Target Type: Instances
Target Group Name: xfusion-tg
Protocol: HTTP
Port: 80
VPC: Default VPC
```

### Health Check Configuration

I configured the health check using:

```text
Health Check Protocol: HTTP
Health Check Path: /
```

The health check allows the Application Load Balancer to monitor the availability of the registered EC2 instance.

### Register Targets

On the Register Targets page, I performed the following steps:

1. Selected the `xfusion-ec2` instance.
2. Set the traffic port to `80`.
3. Clicked **Include as pending below**.
4. Reviewed the registered target.
5. Clicked **Create target group**.

The target group was created successfully.

---

## Step 5: Create Application Load Balancer xfusion-alb

After creating the target group, I created the Application Load Balancer.

I navigated to:

```text
EC2
  |
  v
Load Balancing
  |
  v
Load Balancers
  |
  v
Create Load Balancer
```

I selected:

```text
Application Load Balancer
```

### Basic Configuration

I configured the following settings:

```text
Load Balancer Name: xfusion-alb
Scheme: Internet-facing
IP Address Type: IPv4
```

### Network Mapping

I selected:

```text
VPC: Default VPC
Availability Zones: At least two Availability Zones
Subnets: Public Subnets
```

Selecting subnets in at least two Availability Zones helps provide availability across different zones.

### Security Groups

I attached the default security group to the Application Load Balancer.

I verified that its inbound rules allowed:

| Type | Protocol | Port | Source |
|---|---|---|---|
| HTTP | TCP | 80 | `0.0.0.0/0` |

This allows users to access the load balancer through HTTP.

### Configure Listener and Routing

I configured the listener as follows:

```text
Protocol: HTTP
Port: 80
Default Action: Forward to target group
Target Group: xfusion-tg
```

This configuration allows the ALB to receive incoming HTTP requests and forward them to the registered EC2 instance through the target group.

Finally, I clicked **Create Load Balancer**.

---

## Step 6: Verify Security Group Configuration

After creating the ALB, I checked both security groups.

### ALB Security Group

The default security group attached to the ALB allowed:

```text
Inbound Rule:
Type: HTTP
Port: 80
Source: 0.0.0.0/0
```

### EC2 Security Group

The `xfusion-sg` security group allowed:

```text
Inbound Rule:
Type: HTTP
Port: 80
Source: Default ALB Security Group
```

This configuration ensures that:

- Public users can access the ALB.
- The ALB can communicate with the EC2 instance.
- The EC2 instance does not need to expose HTTP directly to everyone.

---

## Step 7: Verify Target Group Health

After creating the Application Load Balancer, I navigated to:

```text
EC2
  |
  v
Target Groups
  |
  v
xfusion-tg
  |
  v
Targets
```

I checked the health status of the registered EC2 instance.

The expected status was:

```text
Target: xfusion-ec2
Port: 80
Health Status: Healthy
```

The health check may take a few minutes after the initial deployment.

A healthy target confirms that the ALB can communicate with the Nginx web server.

---

## Step 8: Verify Application Load Balancer

I navigated to:

```text
EC2
  |
  v
Load Balancers
  |
  v
xfusion-alb
```

I checked the following information:

```text
Load Balancer Name: xfusion-alb
State: Active
Scheme: Internet-facing
Listener: HTTP:80
Target Group: xfusion-tg
```

I copied the DNS name displayed in the load balancer details.

---

## Step 9: Access the Application Through ALB DNS

To verify that the application was accessible, I opened a web browser and entered the ALB DNS name:

```text
http://<ALB-DNS-NAME>
```

The browser displayed the default **Welcome to nginx!** page.

This confirmed that:

- The EC2 instance was running.
- Nginx was installed and active.
- The target group was configured correctly.
- The target instance was healthy.
- The ALB listener was forwarding HTTP requests.
- The application was accessible through the ALB DNS name.

---

# Verification Using AWS Management Console

I performed the following checks through the AWS Console:

| Resource | Verification | Expected Result |
|---|---|---|
| EC2 Instance | `xfusion-ec2` | Running |
| EC2 Status Checks | Instance checks | 2/2 Passed |
| Security Group | `xfusion-sg` | HTTP 80 from ALB security group |
| Target Group | `xfusion-tg` | Created |
| Target Health | Registered EC2 instance | Healthy |
| Load Balancer | `xfusion-alb` | Active |
| Listener | HTTP : 80 | Forwarding to `xfusion-tg` |
| Browser Access | ALB DNS | Nginx Welcome Page |

---

# Verification Using AWS CLI

After completing the configuration through the AWS Management Console, I used AWS CLI commands to verify the resources.

The verification commands are documented separately in:

```text
commands.md
```

The CLI verification covered:

- EC2 instance status.
- Security group configuration.
- Target group details.
- Registered target health.
- Application Load Balancer status.
- Listener configuration.
- ALB DNS connectivity.

No AWS resources were created using the CLI for this challenge.

---

# Application Load Balancer Workflow

The overall architecture of this challenge was:

```text
             Internet
                |
                |
                v
       xfusion-alb (ALB)
          HTTP : 80
                |
                v
        Listener Configuration
                |
                v
          xfusion-tg
          Target Group
                |
                v
          xfusion-ec2
          EC2 Instance
                |
                v
             Nginx
            HTTP : 80
                |
                v
         Nginx Welcome Page
```

---

# What I Learned

From this challenge, I learned and practiced:

- How to create an EC2 instance using Ubuntu.
- How to configure security groups for web applications.
- How to install Nginx using EC2 User Data.
- How to create an Application Load Balancer.
- How to configure internet-facing load balancers.
- How to create and configure target groups.
- How to register EC2 instances with target groups.
- How ALB listeners work.
- How HTTP traffic is forwarded to backend EC2 instances.
- How target group health checks work.
- How to verify healthy targets through the AWS Console.
- How to verify AWS resources using AWS CLI.
- How load balancing improves application availability and reliability.

# Challenge Status

Day 36 — Completed Successfully ✅

**Created the** **`xfusion-ec2`** **instance, configured Nginx using User Data, created the** **`xfusion-tg`** **target group, and deployed the** **`xfusion-alb`** **Application Load Balancer. Successfully verified the target health and accessed the Nginx web server through the ALB DNS name.**

100 Days. 100 Challenges. One Cloud Journey. ☁️🚀

I will continue documenting each challenge with practical implementation steps, AWS Console verification, CLI verification commands, and lessons learned.
