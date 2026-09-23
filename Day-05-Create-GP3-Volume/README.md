# Day 05 - Create an AWS EBS Volume

## Challenge Overview

This is Day 05 of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The challenge was related to **Amazon Elastic Block Store (EBS) volumes** and required creating a storage volume with specific configuration requirements.

The Nautilus DevOps team is planning to migrate a portion of its infrastructure to the AWS cloud. Instead of performing the migration as one large operation, the team is breaking it down into smaller and manageable tasks.

For this task, I was required to create an Amazon EBS volume with a specific name, volume type, and storage capacity.

### Challenge Requirement

The requirements were:

1. Create an EBS volume named `nautilus-volume`.
2. The volume type must be `gp3`.
3. The volume size must be `2 GiB`.
4. The resource must be created in the `us-east-1` region.

---

## What is Amazon EBS?

**Amazon Elastic Block Store (EBS)** is a block storage service provided by AWS for use with Amazon EC2 instances.

An EBS volume can be attached to an EC2 instance and used as persistent storage for applications, databases, operating systems, and other workloads.

Unlike temporary instance storage, EBS volumes are designed to persist independently from the lifecycle of an EC2 instance.

---

## What is gp3?

`gp3` stands for **General Purpose SSD (gp3)**.

It is an SSD-based Amazon EBS volume type designed for general-purpose workloads.

Some important characteristics of gp3 volumes are:

* SSD-backed storage.
* Designed for general-purpose workloads.
* Provides consistent baseline performance.
* Allows storage capacity and performance to be configured independently.
* Suitable for many applications such as web servers, development environments, and databases.

In this challenge, the required EBS volume type was:

```text
gp3
```

---

## Objectives

The objectives of this challenge are:

* Understand Amazon EBS.
* Learn about AWS block storage.
* Understand the `gp3` EBS volume type.
* Create an EBS volume using the AWS Console.
* Configure an EBS volume with a specific size.
* Work with AWS regions.
* Verify the created EBS volume using the AWS Console and AWS CLI.

---

## Environment

| Item           | Details                   |
| -------------- | ------------------------- |
| Challenge      | 100 Days of Cloud (AWS)   |
| Platform       | KodeKloud                 |
| Day            | 05                        |
| Cloud Provider | Amazon Web Services (AWS) |
| Service        | Amazon EBS                |
| Region         | `us-east-1`               |
| Volume Name    | `nautilus-volume`         |
| Volume Type    | `gp3`                     |
| Volume Size    | `2 GiB`                   |
| Status         | Completed                 |

---

# Solution

## Step 1: Access the AWS Lab Environment

First, I accessed the AWS Management Console using the temporary credentials provided by the KodeKloud lab environment.

I made sure that the credentials were used only for the lab session.

> **Security Note:** AWS credentials should never be added to GitHub repositories or documentation files.

---

## Step 2: Select the Required AWS Region

The challenge required the volume to be created only in:

```text
us-east-1
```

I selected:

```text
US East (N. Virginia) - us-east-1
```

from the AWS region selector.

Using the correct region was important because EBS volumes are regional resources.

---

## Step 3: Open Amazon EC2

From the AWS Management Console, I opened the **EC2** service.

The navigation path was:

```text
AWS Management Console
        |
        v
       EC2
        |
        v
     Volumes
```

---

## Step 4: Open the Volumes Section

Inside the EC2 dashboard, I navigated to:

```text
EC2
  -> Elastic Block Store
  -> Volumes
```

The Volumes section displays the EBS volumes available in the selected AWS region.

---

## Step 5: Create the Volume

I selected **Create volume**.

I configured the volume according to the challenge requirements.

The configuration was:

```text
Volume Type : gp3
Size        : 2 GiB
Region      : us-east-1
```

---

## Step 6: Add the Volume Name

After creating the volume, I added the required name tag:

```text
Name = nautilus-volume
```

The final configuration was:

```text
Name        : nautilus-volume
Volume Type : gp3
Size        : 2 GiB
Region      : us-east-1
```

I then selected **Create volume**.

---

## Step 7: Verify the EBS Volume

After creating the volume, I checked the **Volumes** section.

The volume appeared with the required configuration:

```text
Volume Name : nautilus-volume
Volume Type : gp3
Size        : 2 GiB
Region      : us-east-1
```

The volume was successfully created.

---

# AWS CLI Verification

The EBS volume can also be verified using the AWS CLI.

## Step 1: Verify AWS Identity

I could verify the active AWS lab account using:

```bash
aws sts get-caller-identity
```

---

## Step 2: List EBS Volumes

To list the EBS volumes in `us-east-1`:

```bash
aws ec2 describe-volumes \
    --region us-east-1
```

---

## Step 3: Find the Required Volume

To find the volume using its name tag:

```bash
aws ec2 describe-volumes \
    --region us-east-1 \
    --filters "Name=tag:Name,Values=nautilus-volume"
```

---

## Step 4: Verify Volume Configuration

The volume name, size, type, and state can be displayed using:

```bash
aws ec2 describe-volumes \
    --region us-east-1 \
    --filters "Name=tag:Name,Values=nautilus-volume" \
    --query 'Volumes[*].[VolumeId,Size,VolumeType,State]' \
    --output table
```

Expected information:

```text
------------------------------------------------
|              DescribeVolumes                 |
+----------------+------+-------+--------------+
| Volume ID      | Size | Type  | State        |
+----------------+------+-------+--------------+
| vol-xxxxxxxx   | 2    | gp3   | available    |
+----------------+------+-------+--------------+
```

The exact volume ID will be different for each lab environment.

---

# EBS Volume Configuration

The final configuration of the resource was:

```text
Volume Name : nautilus-volume
Volume Type : gp3
Volume Size : 2 GiB
AWS Region  : us-east-1
```

---

# EBS Volume Workflow

The overall workflow for this challenge was:

```text
AWS Management Console
        |
        v
       EC2
        |
        v
     Volumes
        |
        v
   Create Volume
        |
        +----------------------+
        |                      |
        v                      v
       gp3                    2 GiB
        |                      |
        +----------+-----------+
                   |
                   v
          nautilus-volume
                   |
                   v
          Volume Created
```

---

# What I Learned

From this challenge, I learned and practiced:

* What Amazon EBS is.
* How EBS provides persistent block storage for EC2.
* What the `gp3` volume type is.
* How to create an EBS volume from the AWS Console.
* How to configure EBS volume size.
* How to add a Name tag to an AWS resource.
* How AWS regions affect EBS resources.
* How to verify EBS volumes using the AWS CLI.
* How to inspect volume size, type, and state.

# Challenge Status

Day 05 — Completed Successfully 

**Created the** **`nautilus-volume`** **EBS volume using the** **`gp3`** **volume type with a size of** **2 GiB** **in the** **`us-east-1`** **region.**

100 Days. 100 Challenges. One Cloud Journey. 

I will continue documenting each challenge and building my practical AWS cloud skills step by step.
