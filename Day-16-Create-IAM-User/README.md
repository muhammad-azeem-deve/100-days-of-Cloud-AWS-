# Day 16 - Create IAM User

## Challenge Overview

This is Day 16 of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The challenge was related to **AWS Identity and Access Management (IAM)** and required creating a new IAM user using the AWS Management Console.

IAM (Identity and Access Management) is an AWS service that allows us to securely manage users, permissions, and access to AWS resources.

### Challenge Requirement

The requirement was to:

1. Access the AWS Management Console.
2. Open the **IAM** service.
3. Create a new IAM user as required by the lab.
4. Verify that the IAM user was created successfully using the AWS Console.
5. Verify the created IAM user using AWS CLI commands.

---

## Objectives

The objectives of this challenge are:

* Understand the basics of AWS IAM.
* Learn how to create an IAM user.
* Navigate the IAM Management Console.
* Verify IAM users using the AWS Console.
* Learn how to verify IAM resources using AWS CLI.
* Understand the difference between IAM users and AWS resources.
* Practice basic AWS identity and access management.

---

## Environment

| Item            | Details                   |
| --------------- | ------------------------- |
| Challenge       | 100 Days of Cloud (AWS)   |
| Platform        | KodeKloud                 |
| Day             | 16                        |
| Cloud Provider  | Amazon Web Services (AWS) |
| Service         | AWS IAM                   |
| Resource        | IAM User                  |
| Creation Method | AWS Management Console    |
| Verification    | AWS Console + AWS CLI     |
| Status          | Completed                 |

---

# Solution

## Step 1: Access the AWS Management Console

First, I accessed the AWS Management Console using the credentials provided by the KodeKloud lab environment.

I made sure to use the AWS account and environment provided by the lab.

---

## Step 2: Open the IAM Service

From the AWS Management Console, I searched for:

```text
IAM
```

and opened the **Identity and Access Management (IAM)** service.

The navigation path was:

```text
AWS Management Console
        |
        v
       IAM
        |
        v
      Users
```

---

## Step 3: Open IAM Users

Inside the IAM dashboard, I selected:

```text
Users
```

The IAM Users section displays the users available in the AWS account.

---

## Step 4: Create the IAM User

I selected **Create user**.

I entered the username required by the KodeKloud challenge and completed the required configuration.

The user was created through the AWS Management Console.

The IAM user creation process was completed successfully.

---

## Step 5: Verify the IAM User Using AWS Console

After creating the user, I returned to:

```text
IAM
  |
  └── Users
```

The newly created user appeared in the list of IAM users.

I opened the user details page and verified that the user existed successfully.

The user details page can be used to check information such as:

```text
User name
User ARN
User ID
Creation date
Permissions
Groups
Tags
Access information
```

---

## Step 6: Verify User Details

I opened the newly created IAM user and checked the available information.

The IAM user was successfully visible in the AWS Console.

This confirmed that the user had been created successfully.

---

# AWS CLI Verification

Although the IAM user was created using the AWS Management Console, I used the AWS CLI only for verification.

> **Note:** IAM is a global AWS service, so IAM CLI commands do not require the `--region us-east-1` parameter.

---

## Step 7: List IAM Users

I verified the available IAM users using:

```bash
aws iam list-users
```

The command returned the IAM users available in the AWS account.

---

# What I Learned

From this challenge, I learned and practiced:

* What AWS IAM is.
* How IAM users are created.
* How to navigate the IAM Management Console.
* How to verify an IAM user using the AWS Console.
* How to retrieve IAM user information using AWS CLI.
* How to use `aws iam list-users`.
* How to use `aws iam get-user`.
* Why IAM is important for managing access to AWS resources.
* That IAM is a global AWS service.

# Challenge Status

Day 16 — Completed Successfully 

**Created an IAM user using the AWS Management Console and verified the user successfully using both the AWS Console and AWS CLI.**

100 Days. 100 Challenges. One Cloud Journey. 

I will continue documenting each challenge and building my practical AWS cloud skills step by step.
