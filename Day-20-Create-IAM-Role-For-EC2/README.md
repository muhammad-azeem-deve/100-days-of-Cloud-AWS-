# Day 10 - Create IAM Role for EC2 and Attach Policy

## Challenge Overview

This is Day 10 of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The challenge was related to **AWS Identity and Access Management (IAM), IAM Roles, EC2, and IAM Policies**.

The task was to create an IAM role that can be used by an **Amazon EC2 instance** and attach the required IAM policy to the role.

### Challenge Requirement

The requirements were:

1. Access the AWS Management Console.
2. Open the **IAM** service.
3. Create an IAM role for the **EC2 service**.
4. Attach the required IAM policy to the role.
5. Complete the configuration using the AWS Console.
6. Verify that the IAM role was created successfully.
7. Verify that the required policy was attached to the role.

---

## Objectives

The objectives of this challenge are:

- Understand AWS IAM roles.
- Learn how IAM roles are used with EC2.
- Create a role for an AWS service.
- Configure EC2 as a trusted entity.
- Attach an IAM policy to a role.
- Verify IAM role configuration.
- Verify attached policies using the AWS Console.
- Practice AWS CLI verification commands.

---

## Environment

| Item | Details |
| ----------------- | ------------------------------ |
| Challenge | 100 Days of Cloud (AWS) |
| Platform | KodeKloud |
| Day | 10 |
| Cloud Provider | Amazon Web Services (AWS) |
| Service | AWS IAM |
| Trusted Service | Amazon EC2 |
| Resource Type | IAM Role |
| Policy | Required KodeKloud Lab Policy |
| Region | Global IAM Service |
| Status | Completed |

---

# Solution

## Step 1: Access the AWS Management Console

First, I logged into the AWS Management Console using the temporary credentials provided by the KodeKloud lab environment.

I made sure to use the AWS account provided for the lab.

---

## Step 2: Open IAM

From the AWS Management Console, I opened the **IAM** service.

The navigation path was:

```text
AWS Management Console
        |
        v
       IAM
        |
        v
      Roles
```

---

## Step 3: Open Roles

Inside IAM, I selected:

```text
IAM
  -> Roles
```

The Roles section is used to create and manage IAM roles that can be assumed by AWS services, applications, or users.

---

## Step 4: Create a New Role

I selected:

```text
Create role
```

For the trusted entity type, I selected:

```text
AWS service
```

Then I selected the service:

```text
EC2
```

This configuration allows an EC2 instance to assume the IAM role.

---

## Step 5: Configure the EC2 Trusted Entity

The trusted entity was configured for:

```text
Trusted entity type: AWS service
Service: EC2
Use case: EC2
```

This means that the role can be assumed by EC2 instances.

The trust relationship is an important part of an IAM role because it determines **who or what is allowed to assume the role**.

---

## Step 6: Attach the Required Policy

During role creation, I reached the **Add permissions** step.

I selected the policy required by the KodeKloud task.

The required policy was attached to the IAM role.

The role therefore contained the required permission policy.

> The exact policy name should be the one specified by the corresponding KodeKloud lab environment.

---

## Step 7: Create the IAM Role

After configuring:

```text
Trusted entity: EC2
Permissions: Required IAM Policy
```

I selected:

```text
Create role
```

The IAM role was created successfully.

---

## Step 8: Verify the IAM Role in AWS Console

After creating the role, I opened:

```text
IAM
  -> Roles
```

I located the newly created role and opened it.

The role page displayed:

```text
Role Name
Trusted entities
Permissions policies
```

---

## Step 9: Verify EC2 Trust Relationship

Inside the IAM role, I opened the **Trust relationships** tab.

The trust relationship showed that the EC2 service was allowed to assume the role.

The important service principal was:

```text
ec2.amazonaws.com
```

This confirmed that the role was configured for EC2.

---

## Step 10: Verify Attached Policy

I opened the **Permissions** tab of the IAM role.

The required policy appeared under:

```text
Permissions policies
```

This confirmed that the required policy was successfully attached to the role.

---

# IAM Role Workflow

The overall workflow for this challenge was:

```text
AWS Management Console
        |
        v
       IAM
        |
        v
      Roles
        |
        v
   Create Role
        |
        v
   AWS Service
        |
        v
       EC2
        |
        v
Attach Required Policy
        |
        v
   Create Role
        |
        v
      Verify
```

---

# IAM Role Concept

An IAM role provides temporary permissions to an AWS service or other trusted entity.

For this task:

```text
EC2 Instance
      |
      | Assume Role
      v
  IAM Role
      |
      | Permissions
      v
 IAM Policy
```

The EC2 instance can use the permissions provided by the attached policy without requiring hard-coded AWS access keys.

---

# Verification

I verified the completed task using the AWS Management Console.

### Role Verification

```text
IAM
  -> Roles
  -> Open Created Role
```

I verified:

- The role exists.
- EC2 is the trusted service.
- The role has the required policy attached.

### Trust Relationship Verification

I opened:

```text
Trust relationships
```

and verified that EC2 was listed as the trusted service.

### Policy Verification

I opened:

```text
Permissions
```

and verified that the required IAM policy was attached.

---

# AWS CLI Verification

The resource creation was performed using the **AWS Management Console**.

The AWS CLI was used only to verify the completed configuration.

The verification commands are documented separately in:

```text
commands.md
```

---

# What I Learned

From this challenge, I learned and practiced:

- What an IAM role is.
- The difference between IAM roles and IAM users.
- How IAM roles can be used by EC2.
- How to create an IAM role using the AWS Console.
- How to configure EC2 as a trusted entity.
- How to attach an IAM policy to a role.
- How trust relationships work.
- How IAM policies provide permissions.
- How to verify IAM resources using the AWS Console.
- How to verify IAM resources using AWS CLI commands.
- Why IAM roles are preferred over storing long-term AWS credentials on EC2 instances.

# Challenge Status

Day 10 — Completed Successfully 

**Created an IAM role for EC2, attached the required IAM policy, and verified the role, trust relationship, and permissions using the AWS Management Console.**

100 Days. 100 Challenges. One Cloud Journey. 

I will continue documenting each challenge and building my practical AWS cloud skills step by step.
