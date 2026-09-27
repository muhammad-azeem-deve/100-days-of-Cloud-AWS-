# Day 08 - Enable EC2 Stop Protection

## Challenge Overview

This is Day 08 of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The challenge was related to **Amazon EC2 Instance Protection** and required enabling **Stop Protection** for an existing EC2 instance.

EC2 stop protection helps prevent an instance from being accidentally stopped through the AWS Management Console, CLI, or API.

For this task, I had to locate the required EC2 instance in the AWS environment and enable its **Stop Protection** setting.

### Challenge Requirement

The requirement was:

1. Access the AWS Management Console.
2. Navigate to the **Amazon EC2** service.
3. Locate the required EC2 instance.
4. Enable **Stop Protection** for the instance.
5. Verify that Stop Protection was enabled successfully.
6. Verify the configuration using the AWS CLI.

---

## Objectives

The objectives of this challenge are:

* Understand EC2 instance protection.
* Learn about EC2 Stop Protection.
* Navigate the EC2 Instances dashboard.
* Modify EC2 instance settings using the AWS Console.
* Verify EC2 configuration through the AWS Console.
* Use AWS CLI to verify instance protection.
* Understand how Stop Protection can prevent accidental instance termination through a stop operation.

---

## Environment

| Item           | Details                   |
| -------------- | ------------------------- |
| Challenge      | 100 Days of Cloud (AWS)   |
| Platform       | KodeKloud                 |
| Day            | 08                        |
| Cloud Provider | Amazon Web Services (AWS) |
| Service        | Amazon EC2                |
| Feature        | Stop Protection           |
| Region         | `us-east-1`               |
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

The resource needed to be managed in the required AWS region.

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

I reviewed the available instances and identified the instance provided by the KodeKloud lab for this task.

I selected the required EC2 instance.

---

## Step 4: Open Instance Settings

After selecting the instance, I opened the instance settings menu.

The relevant option was:

```text
Instance
   |
   v
Instance settings
   |
   v
Change stop protection
```

---

## Step 5: Enable Stop Protection

I selected:

```text
Change stop protection
```

A configuration dialog appeared.

I enabled the option for:

```text
Enable
```

and then confirmed the change.

The Stop Protection setting was successfully enabled for the selected EC2 instance.

---

## Step 6: Verify Stop Protection Using AWS Console

After enabling the setting, I selected the EC2 instance again and checked its instance settings.

The Stop Protection configuration showed that protection was enabled.

The expected configuration was:

```text
Stop Protection: Enabled
```

This confirmed that the required setting had been applied successfully.

---

# AWS CLI Verification

After completing the configuration through the AWS Console, I used the AWS CLI to verify the setting.

## Step 7: Verify AWS CLI Access

First, I verified the AWS identity:

```bash
aws sts get-caller-identity
```

This confirmed that the AWS CLI was connected to the KodeKloud lab environment.

---

## Step 8: Find the EC2 Instance

I listed the EC2 instances in the required region:

```bash
aws ec2 describe-instances \
    --region us-east-1 \
    --query 'Reservations[*].Instances[*].[InstanceId,State.Name,Tags[?Key==`Name`]|[0].Value]' \
    --output table
```

This displayed the available EC2 instances along with their instance IDs and states.

---

## Step 9: Check Stop Protection

Once I identified the required instance ID, I verified its Stop Protection configuration using:

```bash
aws ec2 describe-instance-attribute \
    --instance-id <instance-id> \
    --attribute disableApiStop \
    --region us-east-1
```

The expected result was:

```json
{
    "InstanceId": "i-xxxxxxxxxxxxxxxxx",
    "DisableApiStop": true
}
```

The value:

```text
DisableApiStop: true
```

confirms that **EC2 Stop Protection is enabled**.

---

## Step 10: Verify Using a Short CLI Query

The setting can also be checked using:

```bash
aws ec2 describe-instance-attribute \
    --instance-id <instance-id> \
    --attribute disableApiStop \
    --region us-east-1 \
    --query 'DisableApiStop.Value'
```

Expected output:

```text
true
```

This confirms that Stop Protection is enabled.

---

# Stop Protection Verification

The final configuration was:

```text
EC2 Instance
     |
     +-- Region: us-east-1
     |
     +-- Stop Protection: Enabled
     |
     +-- Console Verification: Successful
     |
     +-- CLI Verification: true
```

---

# What I Learned

From this challenge, I learned and practiced:

* How to access and manage EC2 instances.
* How to enable EC2 Stop Protection.
* How instance protection can help prevent accidental stop operations.
* How to modify EC2 instance settings through the AWS Console.
* How to verify EC2 configuration using the AWS Console.
* How to use `describe-instance-attribute` with the AWS CLI.
* How the `DisableApiStop` attribute represents EC2 Stop Protection.
* How to work with EC2 resources in a specific AWS region.

# Challenge Status

Day 08 — Completed Successfully 

**Enabled Stop Protection for the required EC2 instance using the AWS Management Console and verified the configuration successfully using both the AWS Console and AWS CLI.**

100 Days. 100 Challenges. One Cloud Journey. 

I will continue documenting each challenge and building my practical AWS cloud skills step by step.
