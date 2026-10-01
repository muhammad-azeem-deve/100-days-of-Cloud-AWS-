# Day 13 - Create AMI from EC2 Instance

## Challenge Overview

This is Day 13 of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The challenge was related to **Amazon EC2 and Amazon Machine Images (AMI)**.

The task was to create an **Amazon Machine Image (AMI)** from an existing EC2 instance using the AWS Management Console and then verify that the AMI was created successfully.

### Challenge Requirement

The requirement was:

1. Access the AWS Management Console.
2. Navigate to the **EC2** service.
3. Locate the required EC2 instance.
4. Create an AMI from the EC2 instance.
5. Verify that the AMI was created successfully.
6. Use AWS CLI commands only for verification.

---

## Objectives

The objectives of this challenge are:

* Understand what an Amazon Machine Image (AMI) is.
* Create an AMI from an existing EC2 instance.
* Understand how EC2 instances can be used as the source for AMIs.
* Verify the AMI using the AWS Management Console.
* Verify the AMI using AWS CLI commands.
* Understand the relationship between EC2 instances, AMIs, and EBS snapshots.

---

## Environment

| Item            | Details                    |
| --------------- | -------------------------- |
| Challenge       | 100 Days of Cloud (AWS)    |
| Platform        | KodeKloud                  |
| Day             | 13                         |
| Cloud Provider  | Amazon Web Services (AWS)  |
| Service         | Amazon EC2                 |
| Resource        | Amazon Machine Image (AMI) |
| Region          | `us-east-1`                |
| Creation Method | AWS Management Console     |
| Verification    | AWS Console + AWS CLI      |
| Status          | Completed                  |

---

# Solution

## Step 1: Access the AWS Management Console

First, I accessed the AWS Management Console using the credentials provided by the KodeKloud lab environment.

I made sure that the AWS region was set to:

```text
us-east-1
```

The resource had to be created in the required AWS region.

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
     Instances
```

---

## Step 3: Locate the EC2 Instance

Inside the EC2 dashboard, I opened the **Instances** section.

I identified the EC2 instance provided for the KodeKloud task.

Before creating the AMI, I verified that I had selected the correct instance.

The selected instance was used as the source for the new AMI.

---

## Step 4: Create an AMI

After selecting the required EC2 instance, I opened the instance actions menu.

The navigation path was:

```text
Instances
   |
   v
Select EC2 Instance
   |
   v
Actions
   |
   v
Image and templates
   |
   v
Create image
```

I selected **Create image**.

---

## Step 5: Configure the AMI

The **Create image** page was displayed.

I reviewed the AMI configuration and provided the required image information.

The AMI was created from the selected EC2 instance.

The important configuration was:

```text
Source: Selected EC2 Instance
Region: us-east-1
```

I then selected **Create image**.

AWS started the AMI creation process.

---

## Step 6: Open AMIs

After submitting the request, I navigated to:

```text
EC2
  |
  v
Images
  |
  v
AMIs
```

The newly created AMI appeared in the AMIs list.

Initially, the AMI could show a status such as:

```text
Pending
```

I waited until the AMI became available.

---

## Step 7: Verify AMI Status

After the AMI creation process completed, I checked the AMI status.

The status changed to:

```text
Available
```

This confirmed that the AMI had been created successfully.

---

## Step 8: Verify the AMI Details

I selected the newly created AMI and reviewed its details.

I verified information such as:

```text
AMI ID
AMI Name
AMI State
Source Instance
Architecture
Root Device Type
Block Device Mapping
```

The AMI state was:

```text
Available
```

---

## Step 9: Verify Using EC2 Console

The final verification was performed through the AWS EC2 console.

The AMI was visible under:

```text
EC2
  → Images
  → AMIs
```

The AMI showed:

```text
State: Available
```

This confirmed that the task was completed successfully.

---

# AMI Workflow

The overall workflow for this challenge was:

```text
Existing EC2 Instance
        |
        v
     Actions
        |
        v
Image and templates
        |
        v
    Create image
        |
        v
      New AMI
        |
        v
     Pending
        |
        v
     Available
        |
        v
   AMI Verified
```

---

# What I Learned

From this challenge, I learned and practiced:

* What an Amazon Machine Image (AMI) is.
* How to create an AMI from an existing EC2 instance.
* How to use the EC2 **Create image** option.
* How AMIs can be used as templates for launching EC2 instances.
* How to monitor the AMI creation process.
* How to verify an AMI from the AWS Management Console.
* How to verify AMI information using AWS CLI commands.
* The relationship between AMIs and EBS snapshots.

---

# Challenge Status

Day 13 — Completed Successfully 

**Created an Amazon Machine Image (AMI) from an existing EC2 instance using the AWS Management Console and verified that the AMI became available successfully.**

100 Days. 100 Challenges. One Cloud Journey. 

I will continue documenting each challenge and building my practical AWS cloud skills step by step.
