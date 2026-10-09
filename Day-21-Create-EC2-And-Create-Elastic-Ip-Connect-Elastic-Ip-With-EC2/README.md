# Day 21 - Create an EC2 Instance and Assign an Elastic IP

## Challenge Overview

This is Day 21 of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The challenge was related to **Amazon EC2 Instance Creation, Elastic IP Allocation, and Public IP Association**.

The Nautilus DevOps team needed to launch an EC2 instance and assign an Elastic IP address to it. An Elastic IP is a static public IPv4 address that can be associated with an AWS resource, such as an EC2 instance.

In this task, I used the **AWS Management Console** to create the EC2 instance, allocate an Elastic IP address, and associate the Elastic IP with the instance. I then verified the configuration using both the AWS Console and AWS CLI.

### Challenge Requirements

The requirements were:

1. Access the AWS Management Console using the KodeKloud lab environment.
2. Use the `us-east-1` AWS region.
3. Create an EC2 instance with the required lab configuration.
4. Allocate an Elastic IP address.
5. Associate the Elastic IP with the EC2 instance.
6. Verify the EC2 instance and Elastic IP association in the AWS Console.
7. Verify the resources using AWS CLI commands.

---

## Objectives

The objectives of this challenge are:

- Understand the basics of Amazon EC2.
- Learn how to launch an EC2 instance through the AWS Console.
- Understand the purpose of an Elastic IP address.
- Allocate an Elastic IP address in AWS.
- Associate an Elastic IP with an EC2 instance.
- Verify instance and Elastic IP configuration.
- Practice AWS CLI verification commands.
- Understand the difference between a public IP and an Elastic IP.

---

## Environment

| Item | Details |
|---|---|
| Challenge | 100 Days of Cloud (AWS) |
| Platform | KodeKloud |
| Day | 21 |
| Cloud Provider | Amazon Web Services |
| AWS Service | Amazon EC2 |
| Region | `us-east-1` |
| Instance | EC2 instance created through the Console |
| Public IP Resource | Elastic IP |
| Creation Method | AWS Management Console |
| Verification Method | AWS Console and AWS CLI |
| Status | Completed |

> **Note:** The exact instance name, instance type, AMI, and other settings should match the requirements displayed in your KodeKloud lab. They were not specified in the task description, so they are not assumed here.

---

# Solution

## Step 1: Access the AWS Management Console

First, I logged into the AWS Management Console using the credentials provided by the KodeKloud lab.

I selected the required AWS region:

```text
us-east-1
```

All resources for this challenge were created in this region.

---

## Step 2: Open Amazon EC2

From the AWS Management Console, I opened the EC2 service.

The navigation path was:

```text
AWS Management Console
        |
        v
       EC2
        |
        v
    Instances
```

---

## Step 3: Launch an EC2 Instance

I selected **Launch instances** to start creating the EC2 instance.

I configured the instance according to the lab requirements.

The configuration included:

- **Name:** The name required by the lab.
- **AMI:** The required operating system image.
- **Instance type:** The instance type specified by the lab.
- **Key pair:** Selected or created as required.
- **Network settings:** Configured according to the lab requirements.
- **Storage:** Used the required storage configuration.

After reviewing the configuration, I selected **Launch instance**.

---

## Step 4: Verify the EC2 Instance

After launching the instance, I opened the **Instances** section.

I checked that the instance had been created successfully and verified its state.

The instance should reach:

```text
Instance state: Running
```

I also checked the instance ID and other available details.

---

## Step 5: Open Elastic IPs

After creating the EC2 instance, I navigated to the Elastic IP section.

The navigation path was:

```text
EC2 Dashboard
      |
      v
Network & Security
      |
      v
  Elastic IPs
```

An Elastic IP address provides a static public IPv4 address that can be associated with an EC2 instance.

---

## Step 6: Allocate an Elastic IP Address

Inside the **Elastic IPs** section, I selected **Allocate Elastic IP address**.

I kept the allocation settings appropriate for the lab and selected **Allocate**.

AWS allocated an Elastic IP address for my account in the selected region.

The address appeared in the Elastic IPs list.

---

## Step 7: Associate the Elastic IP with the EC2 Instance

After allocating the Elastic IP, I selected the newly allocated address.

I opened the **Actions** menu and selected **Associate Elastic IP address**.

I configured the association:

```text
Resource type: Instance
Instance:      The EC2 instance created in Step 3
Private IP:    The appropriate private IPv4 address
```

I then selected **Associate**.

This connected the Elastic IP address to the EC2 instance.

---

## Step 8: Verify the Association in the AWS Console

I returned to the **Elastic IPs** page and checked the allocated address.

I verified the following information:

- Elastic IP address was allocated.
- The address was associated with an EC2 instance.
- The associated instance ID matched the instance created for this task.
- The address belonged to the `us-east-1` region.

I also opened the EC2 instance details and checked the networking information to confirm that the Elastic IP was assigned.

---

## Step 9: Verify the EC2 Instance Networking Details

I opened the EC2 instance details and checked the networking section.

The expected configuration was:

```text
Instance State: Running
Public IPv4:    Elastic IP address
Private IPv4:   Instance private address
```

The actual IP addresses depend on the resources allocated by AWS.

---

# Verification Using AWS CLI

After completing the task through the AWS Console, I used AWS CLI commands to verify the EC2 instance and Elastic IP association.

All verification commands are documented separately in `commands.md`.

The verification included:

1. Listing EC2 instances in `us-east-1`.
2. Checking the instance state and public IP.
3. Listing allocated Elastic IP addresses.
4. Checking the Elastic IP allocation ID.
5. Verifying the associated EC2 instance ID.
6. Confirming that the Elastic IP and EC2 instance were associated correctly.

---

# EC2 and Elastic IP Workflow

The overall workflow for this challenge was:

```text
AWS Management Console
          |
          v
     Amazon EC2
          |
          v
   Launch EC2 Instance
          |
          v
   Verify Instance State
          |
          v
     Elastic IPs
          |
          v
 Allocate Elastic IP
          |
          v
 Associate with EC2
          |
          v
 Verify Association
          |
          v
   Task Completed
```

---

# What I Learned

From this challenge, I learned and practiced:

- How to launch an EC2 instance using the AWS Management Console.
- How to select the correct AWS region.
- How to allocate an Elastic IP address.
- How to associate an Elastic IP with an EC2 instance.
- How to verify EC2 instance details.
- How to verify Elastic IP allocation and association.
- How to use AWS CLI commands to inspect cloud resources.
- The difference between an automatically assigned public IPv4 address and an Elastic IP address.
- Why a static public IP can be useful when hosting applications on EC2.

---

# Important Notes

- Elastic IP addresses are public IPv4 addresses and should be handled carefully.
- AWS may charge for Elastic IP addresses depending on usage and current pricing policies.
- An Elastic IP that is no longer needed should be disassociated and released to avoid unnecessary charges.
- AWS CLI verification commands must use the same region where the resources were created.
- Never upload AWS credentials, private keys, or secret information to GitHub.

---

# Challenge Status

Day 21 — Completed Successfully 

**Created an EC2 instance, allocated an Elastic IP address, associated the Elastic IP with the instance, and verified the configuration using the AWS Management Console and AWS CLI.**

100 Days. 100 Challenges. One Cloud Journey. 

I will continue documenting each challenge and improving my practical AWS cloud skills step by step.
