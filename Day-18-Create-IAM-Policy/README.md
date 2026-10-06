# Day 18 - Create Read-Only IAM Policy for EC2 Console Access

## Challenge Overview

This is Day 18 of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The challenge was related to **AWS Identity and Access Management (IAM)** and required creating a custom IAM policy that provides **read-only access to Amazon EC2 resources**.

The purpose of the task was to create an IAM policy that allows a user to view EC2 resources from the AWS Console without giving them permission to create, modify, or delete EC2 resources.

### Challenge Requirement

The requirement was to:

1. Access the AWS Management Console.
2. Open the **IAM** service.
3. Create a new customer-managed IAM policy.
4. Provide read-only permissions for Amazon EC2.
5. Create the policy successfully.
6. Verify the policy using the AWS Console.
7. Verify the policy using AWS CLI commands.

---

## Objectives

The objectives of this challenge are:

- Understand AWS IAM policies.
- Understand the difference between read and write permissions.
- Create a customer-managed IAM policy.
- Provide read-only access to Amazon EC2.
- Understand IAM policy JSON structure.
- Verify IAM policies through the AWS Console.
- Verify IAM policies using the AWS CLI.
- Practice the principle of least privilege.

---

## Environment

| Item | Details |
| ----------------- | ------------------------------ |
| Challenge | 100 Days of Cloud (AWS) |
| Platform | KodeKloud |
| Day | 18 |
| Cloud Provider | Amazon Web Services (AWS) |
| Service | AWS IAM |
| Resource | IAM Policy |
| Access | EC2 Read-Only |
| Policy Type | Customer Managed Policy |
| Region | AWS Global |
| Status | Completed |

> **Note:** IAM is a global AWS service, so the policy itself is not restricted to a specific AWS region.

---

# Solution

## Step 1: Access the AWS Management Console

First, I logged into the AWS Management Console using the credentials provided by the KodeKloud lab environment.

After logging in, I opened the **IAM** service.

The navigation path was:

```text
AWS Management Console
        |
        v
       IAM
        |
        v
     Policies
```

---

## Step 2: Open IAM Policies

Inside the IAM dashboard, I selected:

```text
IAM
  -> Policies
```

The Policies page contains AWS managed policies and customer-managed policies.

---

## Step 3: Create a New Policy

I selected:

```text
Create policy
```

I then configured the policy to provide read-only access to Amazon EC2.

---

## Step 4: Configure EC2 Read-Only Permissions

For the policy permissions, I selected the **EC2** service.

The policy was configured to allow read-only EC2 actions.

The policy follows the principle that the user should be able to **view EC2 resources but not modify them**.

The policy permissions are based on EC2 read actions such as:

```text
DescribeInstances
DescribeImages
DescribeVolumes
DescribeSnapshots
DescribeSecurityGroups
DescribeKeyPairs
DescribeNetworkInterfaces
DescribeSubnets
DescribeVpcs
```

These permissions allow EC2 information to be viewed without providing permissions to create, modify, or delete EC2 resources.

---

## Step 5: Review the Policy

Before creating the policy, I reviewed the permissions.

The policy was configured with:

```text
Service:       EC2
Access Level:  Read
Effect:        Allow
```

The policy did not include EC2 write or delete permissions.

---

## Step 6: Name the Policy

I provided a descriptive name for the policy:

```text
EC2ReadOnlyPolicy
```

The policy description was:

```text
Read-only access to Amazon EC2 resources
```

I then selected:

```text
Create policy
```

---

## Step 7: Verify the Policy in AWS Console

After creating the policy, I opened the IAM Policies page and searched for:

```text
EC2ReadOnlyPolicy
```

The policy appeared successfully in the list.

I opened the policy to verify its details.

The policy showed:

```text
Policy Type: Customer managed
Service:     EC2
Access:      Read
Effect:      Allow
```

---

## Step 8: Review Policy Permissions

I opened the policy's **Permissions** section and reviewed the policy JSON.

The policy contained EC2 read-only permissions.

The important point was that the policy did not grant permissions such as:

```text
ec2:RunInstances
ec2:TerminateInstances
ec2:StartInstances
ec2:StopInstances
ec2:ModifyInstanceAttribute
```

This ensures that the policy provides read-only EC2 access rather than administrative access.

---

# Read-Only IAM Policy

The policy concept can be represented using the following JSON:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "ec2:DescribeInstances",
                "ec2:DescribeImages",
                "ec2:DescribeVolumes",
                "ec2:DescribeSnapshots",
                "ec2:DescribeSecurityGroups",
                "ec2:DescribeKeyPairs",
                "ec2:DescribeNetworkInterfaces",
                "ec2:DescribeSubnets",
                "ec2:DescribeVpcs"
            ],
            "Resource": "*"
        }
    ]
}
```

The actual policy was created using the **AWS Management Console**.

---

# Verification

The policy was verified in two ways.

### AWS Console Verification

I verified:

- The policy exists.
- The policy name is correct.
- The policy is a customer-managed policy.
- EC2 permissions are configured.
- The permissions are read-only.
- No EC2 write or delete permissions were included.

### AWS CLI Verification

The AWS CLI was used only to verify the policy after it was created.

The verification commands are documented separately in:

```text
commands.md
```

---

# Principle of Least Privilege

This challenge demonstrated the **Principle of Least Privilege**.

Instead of giving a user full EC2 permissions, the policy provides only the permissions required to view EC2 resources.

For example:

```text
Read EC2 information
        |
        v
     Allowed
```

But:

```text
Create EC2 instance
        |
        v
     Not Allowed
```

```text
Delete EC2 instance
        |
        v
     Not Allowed
```

This reduces the risk of accidental or unauthorized changes to cloud infrastructure.

---

# What I Learned

From this challenge, I learned and practiced:

- How AWS IAM policies work.
- How to create a customer-managed IAM policy.
- How to provide read-only EC2 permissions.
- The difference between read and write permissions.
- How IAM policies use `Effect`, `Action`, and `Resource`.
- How to review IAM policy permissions.
- How to verify IAM policies using the AWS Console.
- How to verify IAM policies using AWS CLI.
- The importance of the Principle of Least Privilege.
- How IAM can be used to control access to AWS resources.

# Challenge Status

Day 18 — Completed Successfully 

**Created and verified a customer-managed IAM policy providing read-only access to Amazon EC2 resources using the AWS Management Console.**

100 Days. 100 Challenges. One Cloud Journey. 

I will continue documenting each AWS challenge and building my practical cloud skills step by step.
