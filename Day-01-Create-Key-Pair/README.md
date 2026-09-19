# Day 01 - AWS EC2 Key Pair

## Challenge Overview

This is Day 01 of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The challenge was related to **AWS EC2 Key Pairs and Cloud Infrastructure Basics**.

The Nautilus DevOps team is planning to migrate a portion of their infrastructure to the AWS cloud. Instead of migrating everything at once, the team decided to break the migration into smaller and more manageable tasks.

For the first task, I was required to create an AWS EC2 key pair with a specific name and key type.

### Challenge Requirement

The requirements were:

1. Access the AWS Management Console using the provided KodeKloud lab environment.
2. Use the **us-east-1** AWS region.
3. Create an EC2 key pair named `datacenter-kp`.
4. The key pair type must be **RSA**.
5. Verify that the key pair was created successfully.

---

## Objectives

The objectives of this challenge are:

* Understand the basics of AWS EC2 key pairs.
* Navigate the AWS Management Console.
* Work with AWS regions.
* Create an EC2 key pair.
* Configure an RSA key pair.
* Verify AWS resources using the AWS Console and CLI.
* Understand how key pairs are used for secure EC2 access.

---

## Environment

| Item           | Details                   |
| -------------- | ------------------------- |
| Challenge      | 100 Days of Cloud (AWS)   |
| Platform       | KodeKloud                 |
| Day            | 01                        |
| Cloud Provider | Amazon Web Services (AWS) |
| Service        | Amazon EC2                |
| Region         | `us-east-1`               |
| Key Pair Name  | `datacenter-kp`           |
| Key Pair Type  | `RSA`                     |
| Status         | Completed                 |

---

# Solution

## Step 1: Access the AWS Lab Environment

First, I accessed the AWS Management Console using the temporary credentials provided by the KodeKloud lab.

The lab environment provided an AWS Console URL and temporary login credentials.

I made sure not to save or publish the credentials in my GitHub repository.

---

## Step 2: Select the Required AWS Region

The challenge specifically required the resource to be created in:

```text
us-east-1
```

I selected **US East (N. Virginia) - us-east-1** from the AWS region selector.

This was important because AWS resources are generally created within a specific region.

---

## Step 3: Open Amazon EC2

From the AWS Management Console, I opened the **EC2** service.

The navigation path was:

```text
AWS Management Console
        |
        v
      EC2
        |
        v
   Key Pairs
```

---

## Step 4: Open Key Pairs

Inside the EC2 dashboard, I navigated to:

```text
EC2
  -> Network & Security
  -> Key Pairs
```

The Key Pairs section allows users to create and manage SSH key pairs that can be associated with EC2 instances.

---

## Step 5: Create the Key Pair

I selected **Create key pair**.

I configured the key pair using the required settings:

```text
Name:           datacenter-kp
Key pair type:  RSA
Region:         us-east-1
```

For the private key file format, I used:

```text
.pem
```

After entering the required configuration, I selected **Create key pair**.

---

## Step 6: Download the Private Key

After the key pair was created, AWS provided the private key file for download.

The private key file was associated with the key pair:

```text
datacenter-kp.pem
```

This private key is sensitive and should be stored securely.

It should **never be uploaded to GitHub**.

---

## Step 7: Verify the Key Pair

After creating the key pair, I checked the EC2 Key Pairs section.

The created key pair appeared with the following configuration:

```text
Key Pair Name : datacenter-kp
Key Type      : RSA
Region        : us-east-1
```

This confirmed that the required AWS resource had been created successfully.

---

# AWS CLI Verification

The same resource can also be verified using the AWS CLI.

## Check AWS CLI Version

```bash
aws --version
```

---

## Verify AWS Identity

I could verify the active AWS lab account using:

```bash
aws sts get-caller-identity
```

This command displays information about the AWS identity currently being used by the CLI.

---

## List EC2 Key Pairs

To list the key pairs in the required region:

```bash
aws ec2 describe-key-pairs --region us-east-1
```

---

## Verify the Required Key Pair

To check the specific key pair:

```bash
aws ec2 describe-key-pairs \
    --key-names datacenter-kp \
    --region us-east-1
```

---

## Verify Key Pair Name and Type

The key pair name and type can be displayed in a table:

```bash
aws ec2 describe-key-pairs \
    --key-names datacenter-kp \
    --region us-east-1 \
    --query 'KeyPairs[0].[KeyName,KeyType]' \
    --output table
```

Expected information:

```text
datacenter-kp    rsa
```

---

# Key Pair Configuration

The final configuration of the resource was:

```text
Key Pair Name : datacenter-kp
Key Pair Type : RSA
AWS Region    : us-east-1
```

---

# AWS EC2 Key Pair Workflow

The overall workflow for this challenge was:

```text
AWS Management Console
        |
        v
      EC2
        |
        v
    Key Pairs
        |
        v
 Create Key Pair
        |
        +-------------------+
        |                   |
        v                   v
 datacenter-kp             RSA
        |
        v
   us-east-1
        |
        v
 Key Pair Created
```

---

# Security Notes

The private key generated during this task is sensitive information.

I did **not** include the private key or AWS credentials in this GitHub repository.

The following files and information should never be committed to a public repository:

```text
*.pem
*.key
AWS Access Key ID
AWS Secret Access Key
AWS Session Token
AWS Console Password
```

If a private key is stored inside the project directory, it should be added to `.gitignore`:

```gitignore
*.pem
*.key
.env
```

---

# What I Learned

From this challenge, I learned and practiced:

* How to access the AWS Management Console.
* How AWS regions work.
* How to navigate the Amazon EC2 dashboard.
* How to create an EC2 key pair.
* How RSA key pairs are used with EC2.
* How private keys are used for secure server access.
* How to verify AWS resources using the AWS CLI.
* Why AWS credentials and private keys must be kept secure.
* The importance of creating resources in the correct AWS region.

# Challenge Status

Day 01 — Completed Successfully 

**Created the** **`datacenter-kp`** **EC2 key pair using the RSA key type in the** **`us-east-1`** **region and verified the resource successfully.**

100 Days. 100 Challenges. One Cloud Journey. 

I will continue documenting each challenge and building my practical AWS cloud skills step by step.
