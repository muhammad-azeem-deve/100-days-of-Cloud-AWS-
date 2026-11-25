# Day 50 - Expand EC2 Root Volume Storage Using AWS EBS

## Challenge Overview

This is Day 50 of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The challenge was related to **Amazon EC2, Elastic Block Store (EBS), Volume Expansion, and Linux Filesystem Management**.

The Nautilus DevOps Team reported that their EC2 instance was running out of storage space. The instance, named `nautilus-ec2`, had an attached EBS volume with a capacity of 8 GiB.

To accommodate growing development data requirements, the team needed to increase the storage capacity from 8 GiB to 12 GiB.

The task involved identifying the EBS volume attached to the EC2 instance, modifying its size through AWS, and extending the root partition and filesystem inside the running Linux instance to utilize the additional storage.

### Challenge Requirement

The requirements were:

1. Access the AWS Management Console using the provided KodeKloud lab environment.
2. Use the `us-east-1` AWS region.
3. Locate the EC2 instance named `nautilus-ec2`.
4. Identify the EBS volume attached to the instance.
5. Increase the EBS volume size from 8 GiB to 12 GiB.
6. Perform the storage expansion without stopping the running EC2 instance.
7. Connect to the instance using SSH.
8. Use the private key located at `/root/nautilus-keypair.pem` on the AWS client machine.
9. Extend the root (`/`) partition and filesystem.
10. Verify that the root filesystem reflects the increased storage capacity.

---

## Objectives

The objectives of this challenge are:

- Understand Amazon EBS volumes.
- Learn how EC2 instances use attached EBS storage.
- Identify EBS volumes attached to an EC2 instance.
- Modify the size of an existing EBS volume.
- Expand storage without stopping an EC2 instance.
- Connect to an EC2 instance using SSH.
- Understand Linux disk partitions and filesystems.
- Extend a root partition using Linux commands.
- Resize a filesystem to utilize additional storage.
- Verify storage changes using Linux disk management commands.

---

## Environment

| Item | Details |
|---|---|
| Challenge | 100 Days of Cloud (AWS) |
| Platform | KodeKloud |
| Day | 50 |
| Cloud Provider | Amazon Web Services (AWS) |
| AWS Service | Amazon EC2 |
| Storage Service | Amazon EBS |
| EC2 Instance | `nautilus-ec2` |
| Initial Volume Size | 8 GiB |
| Required Volume Size | 12 GiB |
| Storage Increase | 4 GiB |
| Root Mount Point | `/` |
| Private Key | `/root/nautilus-keypair.pem` |
| AWS Region | `us-east-1` |
| Connection Method | SSH |
| Status | Completed |

---

# Solution

## Step 1: Access the AWS Client Machine

First, I accessed the AWS client machine provided by the KodeKloud lab environment.

I used the terminal to execute AWS CLI commands and connect to the EC2 instance.

I verified the current user:

```bash
whoami
```

I also checked whether the AWS CLI was installed:

```bash
aws --version
```

## Step 2: Verify AWS Credentials and Region

Before working with AWS resources, I verified the active AWS identity:

```bash
aws sts get-caller-identity
```

The challenge required all AWS operations to be performed in:

```text
us-east-1
```

I configured the default region:

```bash
export AWS_DEFAULT_REGION=us-east-1
```

## Step 3: Locate the EC2 Instance

I opened the AWS Management Console and navigated to the EC2 service.

The navigation path was:

```text
AWS Management Console
        |
        v
       EC2
        |
        v
     Instances
        |
        v
  nautilus-ec2
```

I located the instance named:

```text
nautilus-ec2
```

I verified that the instance was running.

## Step 4: Identify the Attached EBS Volume

After locating the instance, I selected it and opened the **Storage** tab.

The attached EBS volume information was displayed.

The initial volume capacity was:

```text
8 GiB
```

I identified the root EBS volume attached to the EC2 instance.

It was important to identify the correct volume before modifying its size because the root filesystem was the target of this task.

## Step 5: Modify the EBS Volume Size

I navigated to:

```text
EC2
  -> Elastic Block Store
  -> Volumes
```

I selected the EBS volume attached to `nautilus-ec2`.

Then I selected:

```text
Actions
   |
   v
Modify Volume
```

I changed the volume size from:

```text
8 GiB
```

to:

```text
12 GiB
```

After entering the required size, I selected **Modify** and confirmed the modification.

