# Day 14 - Terminate an AWS EC2 Instance

## Challenge Overview

This is Day 14 of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The challenge was related to **Amazon EC2 Instance Management**.

The task was to terminate an existing AWS EC2 instance using the **AWS Management Console** and then verify that the instance had been successfully terminated using both the AWS Console and AWS CLI commands.

### Challenge Requirement

The requirement was:

1. Access the AWS Management Console.
2. Navigate to the Amazon EC2 service.
3. Locate the specified EC2 instance.
4. Terminate the EC2 instance.
5. Verify the instance status from the AWS Console.
6. Verify the termination using AWS CLI commands.

---

## Objectives

The objectives of this challenge are:

* Understand EC2 instance lifecycle management.
* Learn how to terminate an EC2 instance using the AWS Console.
* Understand the difference between stopping and terminating an EC2 instance.
* Verify an EC2 instance's state after termination.
* Use AWS CLI commands to verify the EC2 instance state.
* Practice safe cloud resource management.

---

## Environment

| Item           | Details                   |
| -------------- | ------------------------- |
| Challenge      | 100 Days of Cloud (AWS)   |
| Platform       | KodeKloud                 |
| Day            | 14                        |
| Cloud Provider | Amazon Web Services (AWS) |
| Service        | Amazon EC2                |
| Region         | `us-east-1`               |
| Task           | Terminate EC2 Instance    |
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
   Instances
```

---

## Step 3: Locate the EC2 Instance

Inside the EC2 dashboard, I opened the **Instances** section.

I located the EC2 instance specified by the KodeKloud challenge.

Before terminating it, I verified that I had selected the correct instance.

---

## Step 4: Terminate the EC2 Instance

After selecting the required EC2 instance, I used the instance actions menu:

```text
Instance
   |
   v
Instance state
   |
   v
Terminate instance
```

I confirmed the termination when AWS displayed the confirmation prompt.

---

## Step 5: Wait for Termination

After confirming the action, AWS changed the instance state.

The instance initially entered a termination state and then changed to:

```text
Terminated
```

I waited for the termination process to complete.

---

# Console Verification

After the termination process completed, I returned to the EC2 **Instances** page.

I verified that the selected instance showed:

```text
Instance State: Terminated
```

The instance was no longer running.

The AWS Console therefore confirmed that the EC2 instance had been successfully terminated.

---

# CLI Verification

After verifying the instance through the AWS Console, I used the AWS CLI to verify its final state.

The verification commands are documented separately in:

```text
commands.md
```

The CLI verification confirmed that the EC2 instance had reached the `terminated` state.

---

# Stop vs Terminate

During this task, I also learned the difference between stopping and terminating an EC2 instance.

### Stop

When an EC2 instance is stopped:

```text
Running
   |
   v
Stopped
```

The instance can normally be started again.

### Terminate

When an EC2 instance is terminated:

```text
Running
   |
   v
Terminating
   |
   v
Terminated
```

Termination is a permanent lifecycle action for the EC2 instance.

Therefore, it is important to verify the correct instance before performing this operation.

---

# EC2 Instance Lifecycle

The basic EC2 instance lifecycle can be represented as:

```text
Pending
   |
   v
Running
   |
   +----------+
   |          |
   v          v
Stopped    Terminating
   |          |
   |          v
   +------> Terminated
```

---

# What I Learned

From this challenge, I learned and practiced:

* How to access EC2 instances from the AWS Management Console.
* How to terminate an EC2 instance.
* The difference between stopping and terminating an EC2 instance.
* How to verify an EC2 instance's state from the AWS Console.
* How to use AWS CLI commands to verify EC2 resources.
* The importance of selecting the correct EC2 instance before performing destructive operations.
* How AWS manages the lifecycle of EC2 instances.

# Challenge Status

Day 14 — Completed Successfully 

**Terminated the required AWS EC2 instance using the AWS Management Console and verified that the instance reached the `terminated` state using both the AWS Console and AWS CLI.**

100 Days. 100 Challenges. One Cloud Journey. 

I will continue documenting each challenge and building my practical AWS cloud skills step by step.
