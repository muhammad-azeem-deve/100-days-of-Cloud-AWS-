# Day 10 - Assign an Elastic IP to an EC2 Instance

## Challenge Overview

This is Day 10 of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The challenge was related to **Amazon EC2, Elastic IP addresses, and public IP management**.

The task was to assign a public **Elastic IP address** to an existing EC2 instance. An Elastic IP provides a static public IPv4 address that can remain associated with an AWS resource even when the underlying public IP changes.

For this challenge, I used the **AWS Management Console** to allocate an Elastic IP and associate it with the required EC2 instance.

After completing the task through the AWS Console, I verified the configuration using both the **AWS Console** and the **AWS CLI**.

### Challenge Requirement

The requirement was:

1. Access the AWS Management Console.
2. Navigate to the **EC2** service.
3. Allocate a public Elastic IP address.
4. Associate the Elastic IP with the required EC2 instance.
5. Verify the Elastic IP assignment through the AWS Console.
6. Verify the Elastic IP assignment using AWS CLI commands.

---

## Objectives

The objectives of this challenge are:

* Understand AWS Elastic IP addresses.
* Learn how public IPv4 addresses are managed in AWS.
* Allocate an Elastic IP address.
* Associate an Elastic IP with an EC2 instance.
* Verify EC2 networking information using the AWS Console.
* Verify Elastic IP information using AWS CLI.
* Understand the relationship between an Elastic IP, EC2 instance, and private IP address.

---

## Environment

| Item           | Details                   |
| -------------- | ------------------------- |
| Challenge      | 100 Days of Cloud (AWS)   |
| Platform       | KodeKloud                 |
| Day            | 10                        |
| Cloud Provider | Amazon Web Services (AWS) |
| Service        | Amazon EC2                |
| Resource       | Elastic IP                |
| Assignment     | EC2 Instance              |
| Region         | `us-east-1`               |
| Method         | AWS Management Console    |
| Verification   | AWS Console + AWS CLI     |
| Status         | Completed                 |

---

# Solution

## Step 1: Access the AWS Management Console

First, I accessed the AWS Management Console using the temporary credentials provided by the KodeKloud lab environment.

I made sure that the AWS region was set to:

```text
us-east-1
```

---

## Step 2: Open Amazon EC2

From the AWS Management Console, I opened the **EC2** service.

The navigation path was:

```text
AWS Management Console
        |
        v
      EC2
        |
        v
 Elastic IPs
```

---

## Step 3: Open Elastic IP Addresses

Inside the EC2 dashboard, I navigated to:

```text
EC2
  -> Network & Security
  -> Elastic IPs
```

The Elastic IP section allows AWS users to allocate and manage static public IPv4 addresses.

---

## Step 4: Allocate an Elastic IP

I selected:

```text
Allocate Elastic IP address
```

I kept the required allocation settings and allocated the Elastic IP address.

After allocation, AWS provided a public IPv4 address for the Elastic IP.

The address was initially not associated with an EC2 instance.

---

## Step 5: Associate the Elastic IP

After allocating the Elastic IP, I selected the newly created Elastic IP and chose:

```text
Actions
    |
    v
Associate Elastic IP address
```

I selected the required:

```text
Resource type: Instance
```

Then I selected the required EC2 instance and its private IP address.

Finally, I selected:

```text
Associate
```

The Elastic IP was successfully associated with the EC2 instance.

---

## Step 6: Verify Through EC2 Console

I navigated to:

```text
EC2
  -> Instances
```

I selected the required EC2 instance and checked its networking information.

The instance now displayed the assigned public IPv4 address.

The networking information included:

```text
Private IPv4 Address : <private-ip>
Public IPv4 Address  : <elastic-ip>
```

This confirmed that the Elastic IP had been successfully assigned to the EC2 instance.

---

## Step 7: Verify Through Elastic IP Console

I returned to:

```text
EC2
  -> Network & Security
  -> Elastic IPs
```

The Elastic IP now showed an association with the required EC2 instance.

The information included:

```text
Public IPv4 Address : <elastic-ip>
Instance            : <instance-id>
Private IP Address  : <private-ip>
```

This confirmed the association from the Elastic IP side as well.

---

# AWS CLI Verification

After completing the configuration through the AWS Console, I used the AWS CLI to verify the result.

The verification commands are documented separately in:

```text
commands.md
```

The CLI verification checked:

* Elastic IP address
* Allocation ID
* Association ID
* Instance ID
* Private IP address
* Network interface information

---

# Verification Result

The final configuration was:

```text
Elastic IP       : <elastic-ip>
Instance ID      : <instance-id>
Private IP       : <private-ip>
Association      : Successful
Region           : us-east-1
Status           : Successfully Assigned
```

The exact values depend on the temporary KodeKloud lab environment.

---

# What I Learned

From this challenge, I learned and practiced:

* What an AWS Elastic IP address is.
* The difference between a normal public IPv4 address and an Elastic IP.
* How to allocate an Elastic IP.
* How to associate an Elastic IP with an EC2 instance.
* How to view EC2 networking information.
* How to verify Elastic IP associations from the AWS Console.
* How to verify AWS networking resources using the AWS CLI.
* The importance of working in the correct AWS region.

---

# Elastic IP Workflow

The overall workflow for this challenge was:

```text
AWS Management Console
        |
        v
      EC2
        |
        v
    Elastic IPs
        |
        v
Allocate Elastic IP
        |
        v
Select Elastic IP
        |
        v
Associate Elastic IP
        |
        v
Select EC2 Instance
        |
        v
Association Successful
        |
        v
Verify Using Console
        |
        v
Verify Using AWS CLI
```

---

# Challenge Status

Day 10 — Completed Successfully 

**Allocated a public Elastic IP address and successfully associated it with the required EC2 instance using the AWS Management Console. The assignment was then verified through both the AWS Console and AWS CLI.**

100 Days. 100 Challenges. One Cloud Journey. 

I will continue documenting each AWS challenge and building my practical cloud infrastructure skills step by step.
