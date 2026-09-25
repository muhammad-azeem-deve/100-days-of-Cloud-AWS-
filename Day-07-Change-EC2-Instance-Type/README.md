# Day 07 - Change EC2 Instance Type

## Challenge Overview

This is Day 07 of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The challenge was related to **Amazon EC2 Instance Management, Instance Types, Status Checks, and Resource Optimization**.

During the migration process, the Nautilus DevOps team created several EC2 instances in different regions. As part of their resource optimization efforts, they identified an underutilized EC2 instance and decided to change its instance type.

The task required changing the instance type of the `datacenter-ec2` instance while making sure that the instance status check was completed before making any changes.

### Challenge Requirement

The requirements were:

1. Access the AWS environment using the provided KodeKloud credentials.
2. Work only in the `us-east-1` region.
3. Locate the EC2 instance named `datacenter-ec2`.
4. Make sure the **Status check** is completed before making any changes.
5. Change the instance type from `t2.micro` to `t2.nano`.
6. Make sure the `datacenter-ec2` instance is in the **running** state after the change.
7. Verify the changes using both the AWS Console and AWS CLI.

---

## Objectives

The objectives of this challenge are:

* Understand EC2 instance types.
* Learn how to modify an EC2 instance type.
* Understand why an EC2 instance must be stopped before changing its type.
* Check EC2 instance status checks.
* Start an EC2 instance after modifying its configuration.
* Verify EC2 configuration using the AWS Console.
* Verify EC2 configuration using AWS CLI.
* Understand basic AWS resource optimization.

---

## Environment

| Item                   | Details                   |
| ---------------------- | ------------------------- |
| Challenge              | 100 Days of Cloud (AWS)   |
| Platform               | KodeKloud                 |
| Day                    | 07                        |
| Cloud Provider         | Amazon Web Services (AWS) |
| Service                | Amazon EC2                |
| Region                 | `us-east-1`               |
| Instance Name          | `datacenter-ec2`          |
| Previous Instance Type | `t2.micro`                |
| New Instance Type      | `t2.nano`                 |
| Required State         | `running`                 |
| Status                 | Completed                 |

---

# Solution

## Step 1: Access the AWS Management Console

First, I accessed the AWS Management Console using the temporary credentials provided by the KodeKloud lab environment.

I made sure that the selected AWS region was:

```text
us-east-1
```

The resources for this challenge had to be managed only in the `us-east-1` region.

> **Security Note:** The temporary AWS credentials provided by KodeKloud were used only for the lab and were not included in this GitHub documentation.

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

## Step 3: Locate the datacenter-ec2 Instance

Inside the EC2 **Instances** section, I searched for the instance:

```text
datacenter-ec2
```

I selected the instance and checked its details.

The initial configuration was:

```text
Instance Name : datacenter-ec2
Instance Type : t2.micro
Region        : us-east-1
```

---

## Step 4: Check the Instance Status

Before making any changes, I checked the EC2 instance status.

The instance must complete its status checks before proceeding.

In the EC2 console, I checked:

```text
Status check
```

The required status was:

```text
2/2 checks passed
```

This confirmed that the instance had completed its system and instance status checks.

---

## Step 5: Stop the EC2 Instance

Changing the EC2 instance type requires the instance to be stopped.

From the EC2 console, I selected:

```text
Instance State
      |
      v
    Stop
```

I confirmed the stop operation.

The instance state changed from:

```text
Running
```

to:

```text
Stopping
```

and eventually:

```text
Stopped
```

I waited until the instance reached the **Stopped** state before changing the instance type.

---

## Step 6: Change the Instance Type

After the instance was stopped, I selected:

```text
Actions
   |
   v
Instance settings
   |
   v
Change instance type
```

The original instance type was:

```text
t2.micro
```

I changed it to:

```text
t2.nano
```

The final configuration selected was:

```text
Instance Type: t2.nano
```

I then confirmed the change.

---

## Step 7: Verify the New Instance Type

After changing the instance type, I checked the EC2 instance details.

The configuration showed:

```text
Instance Name : datacenter-ec2
Instance Type : t2.nano
Region        : us-east-1
```

This confirmed that the instance type had been successfully changed from `t2.micro` to `t2.nano`.

---

## Step 8: Start the EC2 Instance

After changing the instance type, I started the instance again.

From the EC2 console, I selected:

```text
Instance State
      |
      v
     Start
```

The instance state changed through:

```text
Pending
```

and eventually:

```text
Running
```

---

## Step 9: Wait for Status Checks

After starting the instance, I waited for the EC2 status checks to complete.

The status checks eventually showed:

```text
2/2 checks passed
```

This confirmed that the instance was healthy and had completed its required status checks.

---

## Step 10: Final Console Verification

I performed a final verification from the EC2 console.

The final configuration was:

```text
Instance Name : datacenter-ec2
Instance Type : t2.nano
Region        : us-east-1
State         : Running
Status Check  : 2/2 checks passed
```

The task was successfully completed.

---

# AWS CLI Verification

After completing the changes through the AWS Console, I used the AWS CLI to verify the EC2 instance configuration.

## Step 11: Verify AWS CLI

First, I checked that AWS CLI was available:

```bash
aws --version
```

---

## Step 12: Verify AWS Identity

I verified the active AWS account:

```bash
aws sts get-caller-identity
```

This confirmed that the AWS CLI was connected to the KodeKloud lab environment.

---

## Step 13: Find the datacenter-ec2 Instance

I used the instance name tag to find the instance:

```bash
aws ec2 describe-instances \
    --region us-east-1 \
    --filters "Name=tag:Name,Values=datacenter-ec2" \
    --query 'Reservations[].Instances[].{InstanceId:InstanceId,Name:Tags[?Key==`Name`]|[0].Value,Type:InstanceType,State:State.Name}' \
    --output table
```

The output should show information similar to:

```text
------------------------------------------------
|              DescribeInstances               |
+----------------------+----------+------------+
| InstanceId           | Type     | State      |
+----------------------+----------+------------+
| i-xxxxxxxxxxxxxxxxx  | t2.nano  | running    |
+----------------------+----------+------------+
```

---

## Step 14: Verify Instance Type

I specifically checked the instance type:

```bash
aws ec2 describe-instances \
    --region us-east-1 \
    --filters "Name=tag:Name,Values=datacenter-ec2" \
    --query 'Reservations[].Instances[].InstanceType' \
    --output text
```

Expected output:

```text
t2.nano
```


---

# Final Configuration

The final EC2 configuration was:

```text
Instance Name : datacenter-ec2
Region        : us-east-1
Old Type      : t2.micro
New Type      : t2.nano
State         : running
Status Check  : 2/2 checks passed
```

---

# EC2 Instance Type Change Workflow

The overall workflow for this challenge was:

```text
datacenter-ec2
      |
      v
Check Status
      |
      v
2/2 Status Checks Passed
      |
      v
Stop Instance
      |
      v
Instance Stopped
      |
      v
Change Type
t2.micro --> t2.nano
      |
      v
Start Instance
      |
      v
Instance Running
      |
      v
2/2 Status Checks Passed
      |
      v
Final Verification
```

---

# What I Learned

From this challenge, I learned and practiced:

* How to locate an EC2 instance using its Name tag.
* How to check EC2 instance status checks.
* Why an EC2 instance needs to be stopped before changing its instance type.
* How to change an EC2 instance type using the AWS Console.
* How to start an EC2 instance after modifying its configuration.
* How to verify an EC2 instance using the AWS CLI.
* How to use AWS CLI filters and queries.
* How to verify the instance state and instance type.
* How EC2 instance types can be adjusted as part of resource optimization.
* The importance of using the correct AWS region when managing resources.

# Challenge Status

Day 07 — Completed Successfully 

**Changed the `datacenter-ec2` instance type from `t2.micro` to `t2.nano`, verified the change using the AWS Console and AWS CLI, and confirmed that the instance returned to the `running` state with its status checks completed.**

100 Days. 100 Challenges. One Cloud Journey. 

I will continue documenting each challenge and building my practical AWS cloud skills step by step.
