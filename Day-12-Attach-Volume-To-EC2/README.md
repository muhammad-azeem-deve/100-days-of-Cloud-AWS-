# Day 12 - Attach EBS Volume to EC2 Instance

## Challenge Overview

This is Day 12 of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The challenge was related to **Amazon EBS Volumes and EC2 Instance Storage Management**.

The task was to attach an existing EBS volume to an EC2 instance using the **AWS Management Console**.

After attaching the volume, I verified the attachment through the AWS Console and then used AWS CLI commands to verify the configuration.

### Challenge Requirement

The requirement was:

1. Access the AWS Management Console.
2. Navigate to the EC2 service.
3. Locate the required EBS volume.
4. Locate the required EC2 instance.
5. Attach the EBS volume to the EC2 instance.
6. Verify the volume attachment using the AWS Console.
7. Verify the attachment using AWS CLI commands.

---

## Objectives

The objectives of this challenge are:

* Understand Amazon EBS volumes.
* Understand how EBS volumes are attached to EC2 instances.
* Work with EC2 instance storage.
* Attach an existing EBS volume to an EC2 instance.
* Verify EBS volume attachment using the AWS Console.
* Verify AWS resources using AWS CLI.
* Understand the relationship between EC2 instances and EBS volumes.

---

## Environment

| Item            | Details                           |
| --------------- | --------------------------------- |
| Challenge       | 100 Days of Cloud (AWS)           |
| Platform        | KodeKloud                         |
| Day             | 12                                |
| Cloud Provider  | Amazon Web Services (AWS)         |
| Service         | Amazon EC2                        |
| Storage Service | Amazon EBS                        |
| Region          | `us-east-1`                       |
| Task            | Attach EBS Volume to EC2 Instance |
| Method          | AWS Management Console            |
| Verification    | AWS Console + AWS CLI             |
| Status          | Completed                         |

---

# Solution

## Step 1: Access the AWS Management Console

First, I accessed the AWS Management Console using the temporary KodeKloud lab environment.

I made sure that the selected AWS region was:

```text
us-east-1
```

The resource had to be managed in the specified region.

---

## Step 2: Open Amazon EC2

From the AWS Management Console, I opened the **EC2** service.

The navigation path was:

```text
AWS Management Console
        |
        v
      EC2
```

---

## Step 3: Open the Volumes Section

Inside the EC2 dashboard, I navigated to:

```text
EC2
  -> Elastic Block Store
  -> Volumes
```

The **Volumes** section displays the EBS volumes available in the selected AWS region.

---

## Step 4: Identify the Required EBS Volume

I located the EBS volume that needed to be attached to the EC2 instance.

I checked the volume details, including:

```text
Volume ID
Volume Type
Size
Availability Zone
State
```

The volume needed to be in an attachable state.

For an EBS volume that is not currently attached, the state is generally:

```text
available
```

---

## Step 5: Identify the Required EC2 Instance

I navigated to:

```text
EC2
  -> Instances
  -> Instances
```

I located the EC2 instance specified by the KodeKloud challenge.

I verified the instance details before attaching the volume.

Important information included:

```text
Instance ID
Instance State
Availability Zone
```

The EBS volume and EC2 instance must be in compatible Availability Zones for attachment.

---

## Step 6: Attach the EBS Volume

I returned to the **Volumes** section.

I selected the required EBS volume and chose:

```text
Actions
   -> Attach volume
```

The attach volume dialog was displayed.

I selected the required EC2 instance and confirmed the attachment.

The configuration was then submitted.

---

## Step 7: Verify Attachment in AWS Console

After attaching the volume, I opened the volume details again.

The volume's state changed from:

```text
available
```

to:

```text
in-use
```

The volume details also displayed the associated EC2 instance.

The attachment information confirmed that the EBS volume was successfully attached.

---

## Step 8: Verify from the EC2 Instance

I also checked the EC2 instance's storage information.

The attached EBS volume appeared under the instance's storage/block device information.

This confirmed that the EC2 instance was associated with the required EBS volume.

---

# Console Verification

The final configuration showed:

```text
EBS Volume
    |
    +-- State: in-use
    |
    +-- Attached to: Required EC2 Instance
    |
    +-- Region: us-east-1
```

The AWS Console confirmed that the volume was successfully attached to the EC2 instance.

---

# AWS CLI Verification

After completing the task through the AWS Management Console, I used AWS CLI commands to verify the attachment.

The verification commands are documented separately in:

```text
commands.md
```

The CLI verification checked:

* EBS volume state.
* EC2 instance attachment.
* Volume ID.
* Instance ID.
* Device name.
* Attachment state.

---

# EBS Volume Attachment Workflow

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
 Select EBS Volume
        |
        v
  Attach Volume
        |
        v
 Select EC2 Instance
        |
        v
     Attach
        |
        v
 Volume State: in-use
        |
        v
 Verify in Console
        |
        v
 Verify using AWS CLI
```

---

# What I Learned

From this challenge, I learned and practiced:

* How Amazon EBS volumes work.
* How EBS provides block storage for EC2 instances.
* How to attach an existing EBS volume to an EC2 instance.
* How to check an EBS volume's state.
* How to verify volume attachment from the EC2 Console.
* How EC2 instances and EBS volumes are associated.
* How to verify AWS resources using AWS CLI.
* Why the EBS volume and EC2 instance need to be in compatible Availability Zones.

# Challenge Status

Day 12 — Completed Successfully 

**Attached the required EBS volume to the EC2 instance using the AWS Management Console and verified the attachment successfully using both the AWS Console and AWS CLI.**

100 Days. 100 Challenges. One Cloud Journey. 

I will continue documenting each challenge and building my practical AWS cloud skills step by step.
