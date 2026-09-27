# Day 09 - Enable EC2 Termination Protection

## Challenge Overview

This is Day 09 of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The challenge was related to **Amazon EC2 Termination Protection**.

The task was to protect an existing EC2 instance from accidental termination by enabling **Termination Protection** through the AWS Management Console.

After enabling the protection, I also verified the configuration using both the **AWS Console** and the **AWS CLI**.

### Challenge Requirement

The requirement was:

1. Access the AWS Management Console.
2. Use the `us-east-1` AWS region.
3. Locate the required EC2 instance.
4. Enable **Termination Protection** for the instance.
5. Verify the configuration from the AWS Console.
6. Verify the configuration using the AWS CLI.

---

## Objectives

The objectives of this challenge are:

* Understand EC2 termination protection.
* Learn how to protect an EC2 instance from accidental termination.
* Configure termination protection using the AWS Management Console.
* Understand the `DisableApiTermination` EC2 attribute.
* Verify EC2 instance attributes using the AWS CLI.
* Practice AWS resource management and protection mechanisms.

---

## Environment

| Item           | Details                   |
| -------------- | ------------------------- |
| Challenge      | 100 Days of Cloud (AWS)   |
| Platform       | KodeKloud                 |
| Day            | 09                        |
| Cloud Provider | Amazon Web Services (AWS) |
| Service        | Amazon EC2                |
| Region         | `us-east-1`               |
| Instance Name  | `devops-ec2`              |
| Protection     | Termination Protection    |
| Attribute      | `DisableApiTermination`   |
| Status         | Completed                 |

---

# Solution

## Step 1: Access the AWS Management Console

First, I accessed the AWS Management Console using the temporary credentials provided by the KodeKloud lab environment.

I made sure that the selected AWS region was:

```text
us-east-1
```

The region was important because the EC2 instance was created in the specified AWS region.

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

I located the required instance:

```text
devops-ec2
```

I selected the instance to work with.

---

## Step 4: Open Instance Settings

After selecting the EC2 instance, I opened:

```text
Actions
   |
   v
Instance settings
   |
   v
Change termination protection
```

AWS provides this option to enable or disable termination protection for the selected instance.

---

## Step 5: Enable Termination Protection

In the **Change termination protection** window, I selected:

```text
Enable
```

Then I selected:

```text
Save
```

The termination protection setting was successfully enabled.

---

## Step 6: Understand Termination Protection

Termination protection prevents an EC2 instance from being terminated through the AWS EC2 Console, AWS CLI, or API while the protection is enabled. AWS implements this using the `DisableApiTermination` instance attribute.

The configuration can be represented as:

```text
Termination Protection
        |
        v
DisableApiTermination = true
```

---

# Console Verification

## Step 7: Verify Termination Protection in the AWS Console

After saving the configuration, I selected the EC2 instance again and opened:

```text
Actions
   |
   v
Instance settings
   |
   v
Change termination protection
```

The setting showed that termination protection was enabled.

This confirmed that the EC2 instance was protected against accidental termination.

---

# AWS CLI Verification

## Step 8: Check the AWS CLI

I first verified that the AWS CLI was available:

```bash
aws --version
```

---

## Step 9: Verify AWS Identity

I verified the current AWS lab account:

```bash
aws sts get-caller-identity
```

This confirmed that the AWS CLI was using the lab credentials.

---

## Step 10: Find the EC2 Instance ID

I used the instance name to find the EC2 instance ID:

```bash
aws ec2 describe-instances \
    --filters "Name=tag:Name,Values=devops-ec2" \
    --region us-east-1 \
    --query 'Reservations[].Instances[].[InstanceId,Tags[?Key==`Name`].Value|[0],State.Name]' \
    --output table
```

The output provided information similar to:

```text
-------------------------------------------------
|              DescribeInstances                |
+----------------------+------------+------------+
| Instance ID          | Name       | State      |
+----------------------+------------+------------+
| i-xxxxxxxxxxxxxxxxx  | devops-ec2 | running    |
+----------------------+------------+------------+
```

I used the returned instance ID for the verification command.

---

## Step 11: Verify Termination Protection Using CLI

I checked the `disableApiTermination` attribute using:

```bash
aws ec2 describe-instance-attribute \
    --instance-id <INSTANCE-ID> \
    --attribute disableApiTermination \
    --region us-east-1
```

Expected output:

```json
{
    "InstanceId": "i-xxxxxxxxxxxxxxxxx",
    "DisableApiTermination": {
        "Value": true
    }
}
```

The important value was:

```text
Value: true
```

This confirmed that termination protection was enabled.

---

## Step 12: Verify with a Simple CLI Query

The attribute can also be checked using:

```bash
aws ec2 describe-instance-attribute \
    --instance-id <INSTANCE-ID> \
    --attribute disableApiTermination \
    --region us-east-1 \
    --query 'DisableApiTermination.Value' \
    --output text
```

Expected result:

```text
True
```

This provided a quick confirmation that termination protection was enabled.

---

# Termination Protection Workflow

The overall workflow for this challenge was:

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
   devops-ec2
        |
        v
Actions → Instance settings
        |
        v
Change termination protection
        |
        v
       Enable
        |
        v
       Save
        |
        v
DisableApiTermination = true
        |
        v
CLI Verification
```

---

# Important Note

Termination protection is specifically designed to prevent accidental termination through the EC2 Console, CLI, or API. It does not mean that an EC2 instance can never be terminated under every circumstance. For example, AWS documents exceptions such as scheduled events, and termination protection cannot be enabled for Spot Instances.

---

# What I Learned

From this challenge, I learned and practiced:

* How EC2 termination protection works.
* How to enable termination protection from the AWS Console.
* How to navigate EC2 instance settings.
* How the `DisableApiTermination` attribute controls termination protection.
* How to find an EC2 instance using AWS CLI filters.
* How to verify EC2 instance attributes using AWS CLI.
* The difference between protecting an instance and terminating an instance.
* How AWS provides safeguards against accidental infrastructure changes.

# Challenge Status

Day 09 — Completed Successfully 

**Enabled Termination Protection for the** **`devops-ec2`** **EC2 instance in the** **`us-east-1`** **region and verified the configuration using both the AWS Management Console and AWS CLI.**

100 Days. 100 Challenges. One Cloud Journey. 

I will continue documenting each challenge and building my practical AWS cloud skills step by step.
