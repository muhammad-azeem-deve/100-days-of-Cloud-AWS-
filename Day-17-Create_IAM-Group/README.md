# Day 17 - Create IAM Group

## Challenge Overview

This is Day 17 of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The challenge was related to **AWS Identity and Access Management (IAM)**.

IAM is an AWS service that helps manage users, groups, roles, and permissions securely. In this task, I was required to create an IAM group using the AWS Management Console and then verify that the group was created successfully.

### Challenge Requirement

The requirement was:

1. Access the AWS Management Console.
2. Open the **IAM** service.
3. Create a new IAM group.
4. Use the group name provided by the lab.
5. Verify the IAM group using the AWS Console.
6. Verify the group using AWS CLI commands.

---

## Objectives

The objectives of this challenge are:

* Understand the basics of AWS IAM.
* Learn about IAM groups.
* Create an IAM group using the AWS Management Console.
* Navigate the IAM Groups section.
* Verify an IAM group from the AWS Console.
* Use AWS CLI commands to verify IAM resources.

---

## Environment

| Item            | Details                   |
| --------------- | ------------------------- |
| Challenge       | 100 Days of Cloud (AWS)   |
| Platform        | KodeKloud                 |
| Day             | 17                        |
| Cloud Provider  | Amazon Web Services (AWS) |
| Service         | AWS IAM                   |
| Resource        | IAM Group                 |
| Group Name      | `<GROUP_NAME>`            |
| Creation Method | AWS Management Console    |
| Verification    | AWS Console + AWS CLI     |
| Status          | Completed                 |

---

# Solution

## Step 1: Access the AWS Management Console

First, I logged into the AWS Management Console using the temporary credentials provided by the KodeKloud lab environment.

I made sure to use only the credentials provided for the lab and did not save them in the GitHub repository.

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
      User Groups
```

---

## Step 3: Open User Groups

Inside the IAM dashboard, I selected:

```text
IAM
  -> User groups
```

The **User groups** section contains the IAM groups available in the AWS account.

---

## Step 4: Create IAM Group

I selected **Create group**.

I entered the group name provided by the KodeKloud task:

```text
<GROUP_NAME>
```

No additional permissions were added unless specifically required by the lab.

I then selected **Create group**.

---

## Step 5: Verify the IAM Group Using AWS Console

After creating the group, I returned to:

```text
IAM
  -> User groups
```

The newly created group appeared in the list.

I verified:

```text
Group Name: <GROUP_NAME>
```

This confirmed that the IAM group was created successfully.

---

# Verification Using AWS CLI

Although the IAM group was created using the AWS Management Console, I also verified the resource using AWS CLI.

## List IAM Groups

I used the following command:

```bash
aws iam list-groups
```

This displays the IAM groups available in the AWS account.

---

## Verify the Specific IAM Group

To verify the required group:

```bash
aws iam get-group --group-name <GROUP_NAME>
```

If the group exists, AWS returns information about the group.

---

## Display Only the Group Name

To display only the group name:

```bash
aws iam get-group \
    --group-name <GROUP_NAME> \
    --query 'Group.GroupName' \
    --output text
```

Expected output:

```text
<GROUP_NAME>
```

---

## Verify IAM Group Details

The following command can be used to display the group's details:

```bash
aws iam get-group \
    --group-name <GROUP_NAME> \
    --query 'Group.[GroupName,GroupId,Arn]' \
    --output table
```

The output confirms that the group exists and displays its:

* Group Name
* Group ID
* ARN

---

# IAM Group Workflow

The overall workflow for this challenge was:

```text
AWS Management Console
        |
        v
       IAM
        |
        v
   User Groups
        |
        v
   Create Group
        |
        v
 <GROUP_NAME>
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

* What AWS IAM is.
* What IAM groups are used for.
* How to create an IAM group using the AWS Management Console.
* How IAM groups can be used to manage permissions for multiple users.
* How to verify IAM resources from the AWS Console.
* How to verify IAM groups using AWS CLI.
* How to retrieve IAM group information using `aws iam get-group`.

# Challenge Status

Day 17 — Completed Successfully 

**Created the required IAM group using the AWS Management Console and verified its existence through both the AWS Console and AWS CLI.**

100 Days. 100 Challenges. One Cloud Journey. 

I will continue documenting each AWS Cloud challenge and building my practical cloud skills step by step.
