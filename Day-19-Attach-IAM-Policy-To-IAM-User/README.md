# Day 19 - Attach Existing IAM Policy to Existing IAM User

## Challenge Overview

This is Day 19 of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The challenge was related to **AWS Identity and Access Management (IAM)** and required attaching an existing IAM policy to an existing IAM user.

The task focused on managing permissions for an IAM user by attaching an already existing policy instead of creating a new policy.

### Challenge Requirement

The requirement was:

1. Access the AWS Management Console.
2. Navigate to the **IAM** service.
3. Locate the existing IAM user provided by the lab.
4. Locate the existing IAM policy provided by the lab.
5. Attach the existing IAM policy to the existing IAM user.
6. Verify that the policy was successfully attached to the user.
7. Verify the configuration using AWS CLI commands.

---

## Objectives

The objectives of this challenge are:

- Understand AWS Identity and Access Management (IAM).
- Understand IAM users and policies.
- Learn how IAM policies provide permissions.
- Attach an existing policy to an existing IAM user.
- Verify IAM user permissions through the AWS Console.
- Verify IAM configuration using AWS CLI.
- Practice managing AWS permissions securely.

---

## Environment

| Item | Details |
| ----------------- | ------------------------------ |
| Challenge | 100 Days of Cloud (AWS) |
| Platform | KodeKloud |
| Day | 19 |
| AWS Service | IAM |
| Resource | Existing IAM User |
| Policy | Existing IAM Policy |
| Method | AWS Management Console |
| Verification | AWS Console + AWS CLI |
| Status | Completed |

---

# Solution

## Step 1: Access the AWS Management Console

First, I accessed the AWS Management Console using the credentials provided by the KodeKloud lab environment.

I made sure that I was working in the AWS environment provided by the lab.

---

## Step 2: Open IAM

From the AWS Management Console, I searched for and opened:

```text
IAM
```

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

This displayed the existing IAM users available in the lab environment.

I located the IAM user specified by the challenge.

---

## Step 4: Open the Existing IAM User

I selected the required IAM user.

The user's IAM page contains several sections, including:

```text
Permissions
Groups
Tags
Security credentials
Access Advisor
```

I opened the **Permissions** section.

---

## Step 5: Add Permissions

Inside the user's **Permissions** section, I selected:

```text
Add permissions
```

AWS provided several options for assigning permissions.

I selected the option to attach policies directly to the user.

---

## Step 6: Select the Existing IAM Policy

I searched for the existing IAM policy specified by the KodeKloud task.

I selected the required existing policy from the list.

The policy was **not created from scratch** because the challenge specifically required attaching an existing IAM policy.

---

## Step 7: Attach the Policy

After selecting the required policy, I selected:

```text
Add permissions
```

AWS then attached the selected policy to the IAM user.

---

## Step 8: Verify Using the AWS Console

After attaching the policy, I returned to the IAM user's:

```text
Permissions
```

section.

The attached policy appeared under the user's permissions.

The final configuration was:

```text
IAM User
    |
    +---- Existing IAM Policy
              |
              +---- Attached Successfully
```

This confirmed that the existing policy had been successfully attached to the existing IAM user.

---

# Verification

The configuration was verified using both the **AWS Management Console** and the **AWS CLI**.

## Console Verification

I opened:

```text
IAM
  -> Users
  -> <IAM User>
  -> Permissions
```

The required existing IAM policy was visible in the user's permissions list.

This confirmed that the policy was attached successfully.

---

## CLI Verification

The AWS CLI can be used to verify the policies attached directly to an IAM user.

The verification commands are documented separately in:

```text
commands.md
```

The CLI verification checks the user's attached policies and confirms that the required policy is associated with the user.

---

# IAM Permission Workflow

The overall workflow for this challenge was:

```text
AWS Management Console
        |
        v
       IAM
        |
        v
      Users
        |
        v
 Existing IAM User
        |
        v
    Permissions
        |
        v
 Add Permissions
        |
        v
Attach Existing Policy
        |
        v
  Verify Policy
```

---

# What I Learned

From this challenge, I learned and practiced:

- How AWS IAM manages access and permissions.
- The difference between IAM users and IAM policies.
- How policies define permissions in AWS.
- How to attach an existing policy to an IAM user.
- How to manage IAM permissions through the AWS Console.
- How to verify attached IAM policies.
- How AWS CLI can be used to verify IAM configuration.
- Why IAM permissions should be managed carefully and securely.

---

# Important IAM Security Notes

IAM permissions should follow the principle of **least privilege**.

Users should receive only the permissions they actually need.

I also learned that AWS credentials and sensitive IAM information should never be committed to a public GitHub repository.

Never publish:

```text
AWS Access Key ID
AWS Secret Access Key
AWS Session Token
AWS Console Password
```

---

# Challenge Status

Day 19 — Completed Successfully 

**Attached the existing IAM policy to the existing IAM user using the AWS Management Console and verified the configuration using the AWS Console and AWS CLI.**

100 Days. 100 Challenges. One Cloud Journey. 

I will continue documenting each AWS challenge and building my practical cloud skills step by step.
