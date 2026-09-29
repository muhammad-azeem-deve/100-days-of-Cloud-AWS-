# Day 11 - Attach Elastic Network Interface to EC2 Instance

## Challenge Overview

This is Day 11 of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The challenge was related to **Amazon EC2 and Elastic Network Interfaces (ENIs)**.

An Elastic Network Interface is a virtual network interface that can be attached to an EC2 instance. It provides network connectivity and can have attributes such as a private IP address, security groups, and a MAC address.

The task was to attach the required **Elastic Network Interface (ENI)** to an existing **EC2 instance** using the AWS Management Console.

### Challenge Requirement

The requirement was:

1. Access the AWS Management Console.
2. Use the required AWS region.
3. Locate the required Elastic Network Interface.
4. Locate the required EC2 instance.
5. Attach the Elastic Network Interface to the EC2 instance.
6. Verify that the ENI was successfully attached using the AWS Console.
7. Verify the attachment using the AWS CLI.

---

## Objectives

The objectives of this challenge are:

* Understand Elastic Network Interfaces in AWS.
* Learn how ENIs are associated with EC2 instances.
* Attach an existing ENI to an EC2 instance.
* Verify network interface attachment from the AWS Console.
* Verify the ENI attachment using AWS CLI.
* Understand the relationship between EC2 instances and network interfaces.

---

## Environment

| Item           | Details                   |
| -------------- | ------------------------- |
| Challenge      | 100 Days of Cloud (AWS)   |
| Platform       | KodeKloud                 |
| Day            | 11                        |
| Cloud Provider | Amazon Web Services (AWS) |
| Service        | Amazon EC2                |
| Resource       | Elastic Network Interface |
| Region         | `us-east-1`               |
| Method         | AWS Management Console    |
| Verification   | AWS Console + AWS CLI     |
| Status         | Completed                 |

---

# Solution

## Step 1: Access the AWS Management Console

First, I accessed the AWS Management Console using the temporary KodeKloud lab environment.

I made sure that I was working in the required AWS region.

```text
us-east-1
```

---

## Step 2: Open the EC2 Dashboard

From the AWS Management Console, I opened the **EC2** service.

The navigation path was:

```text
AWS Management Console
        |
        v
       EC2
        |
        v
Network Interfaces
```

---

## Step 3: Locate the Elastic Network Interface

Inside the EC2 dashboard, I opened:

```text
Network Interfaces
```

I located the Elastic Network Interface that needed to be attached to the EC2 instance.

I checked the ENI details before attaching it.

Important information included:

```text
Network Interface ID
Subnet ID
Private IPv4 Address
Security Groups
Availability Zone
```

---

## Step 4: Locate the EC2 Instance

I navigated to:

```text
EC2
  -> Instances
```

I located the EC2 instance specified by the KodeKloud challenge.

Before attaching the ENI, I verified that I had selected the correct instance.

---

## Step 5: Attach the Elastic Network Interface

From the **Network Interfaces** section, I selected the required ENI.

I then selected:

```text
Actions
   -> Attach
```

I selected the required EC2 instance as the attachment target.

After confirming the required settings, I selected **Attach**.

The ENI was successfully attached to the EC2 instance.

---

## Step 6: Verify Using the AWS Console

After attaching the ENI, I opened the EC2 instance details.

I checked the **Networking** section of the instance.

The attached network interface appeared under the instance's network interfaces.

I verified:

```text
Network Interface ID
Private IPv4 Address
Subnet
Security Groups
Attachment Status
```

The ENI showed that it was attached to the selected EC2 instance.

---

## Step 7: Verify from Network Interfaces

I also returned to:

```text
EC2
  -> Network Interfaces
```

I selected the ENI and checked its details.

The attachment information showed the EC2 instance associated with the ENI.

This confirmed that the ENI was successfully attached.

---

# AWS CLI Verification

After completing the task through the AWS Console, I used the AWS CLI to verify the configuration.

The CLI verification commands are documented separately in:

```text
commands.md
```

The verification checked:

* ENI ID
* Attached EC2 instance
* Attachment ID
* Attachment status
* Private IP address
* Subnet
* Availability Zone

---

# Elastic Network Interface Workflow

The overall workflow for this challenge was:

```text
AWS Management Console
        |
        v
      EC2
        |
        +----------------------+
        |                      |
        v                      v
Network Interfaces         Instances
        |                      |
        v                      v
Select ENI              Select EC2 Instance
        |                      |
        +----------+-----------+
                   |
                   v
              Attach ENI
                   |
                   v
             Verify Attachment
                   |
          +--------+--------+
          |                 |
          v                 v
     AWS Console         AWS CLI
```

---

# What I Learned

From this challenge, I learned and practiced:

* What an Elastic Network Interface is.
* How ENIs provide network connectivity to EC2 instances.
* How to locate an existing ENI.
* How to attach an ENI to an EC2 instance.
* How to verify network interface information from the AWS Console.
* How to verify ENI attachment using AWS CLI.
* How EC2 instances and network interfaces are connected.
* How to inspect private IP addresses, subnets, and security groups associated with an ENI.

# Challenge Status

Day 11 — Completed Successfully 

**Successfully attached the required Elastic Network Interface to the EC2 instance using the AWS Management Console and verified the attachment using both the AWS Console and AWS CLI.**

100 Days. 100 Challenges. One Cloud Journey. 

I will continue documenting each AWS challenge and building my practical cloud skills step by step.
