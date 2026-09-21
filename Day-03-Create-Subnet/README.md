# Day 03 - Create AWS Subnet

## Challenge Overview

This is Day 03 of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The challenge was related to **AWS VPCs, Subnets, and Network Infrastructure**.

The Nautilus DevOps team is planning to migrate a portion of their infrastructure to the AWS cloud. Instead of performing the migration as one large operation, the team is breaking the migration into smaller and manageable tasks.

For this task, I was required to create a subnet inside the **default VPC**.

### Challenge Requirement

The requirements were:

1. Access the AWS lab environment.
2. Use the **us-east-1** AWS region.
3. Identify the **default VPC**.
4. Create one subnet under the default VPC.
5. Name the subnet `devops-subnet`.
6. Verify the subnet using the AWS CLI.
7. Use the **VPC ID filter** with `describe-subnets` during verification.

---

## Objectives

The objectives of this challenge are:

* Understand the relationship between VPCs and subnets.
* Identify the default VPC.
* Create a subnet inside an existing VPC.
* Work with AWS networking resources.
* Use AWS CLI commands to manage networking resources.
* Verify AWS resources using `describe-subnets`.
* Understand how AWS resource filtering works.
* Practice working with AWS regions.

---

## Environment

| Item           | Details                   |
| -------------- | ------------------------- |
| Challenge      | 100 Days of Cloud (AWS)   |
| Platform       | KodeKloud                 |
| Day            | 03                        |
| Cloud Provider | Amazon Web Services (AWS) |
| Service        | Amazon VPC                |
| Region         | `us-east-1`               |
| VPC            | Default VPC               |
| Subnet Name    | `devops-subnet`           |
| Status         | Completed                 |

---

# Solution

## Step 1: Access the AWS Client

First, I accessed the AWS client machine provided by the KodeKloud lab.

The lab provides temporary AWS credentials that can be retrieved using:

```bash
showcreds
```

I used these temporary credentials for the AWS lab environment.

> **Security Note:** AWS credentials were used only for the lab and were not added to the GitHub repository.

---

## Step 2: Verify AWS Credentials

After retrieving the temporary credentials, I verified that the AWS CLI was connected to the correct AWS account:

```bash
aws sts get-caller-identity
```

This command confirms the AWS identity associated with the currently configured credentials.

---

## Step 3: Verify the AWS Region

The challenge required all resources to be created in:

```text
us-east-1
```

I made sure that the AWS CLI was using the required region.

The region can be specified explicitly with AWS CLI commands:

```bash
--region us-east-1
```

---

## Step 4: Find the Default VPC

Before creating the subnet, I identified the default VPC in the `us-east-1` region.

I used:

```bash
aws ec2 describe-vpcs \
    --region us-east-1 \
    --filters Name=isDefault,Values=true
```

This command returns information about the default VPC.

The important value required for the next step was:

```text
VpcId
```

For example:

```text
vpc-xxxxxxxxxxxxxxxxx
```

The actual VPC ID depends on the KodeKloud lab environment.

---

## Step 5: Check Existing Subnets

Before creating a new subnet, I checked the existing subnets associated with the default VPC:

```bash
aws ec2 describe-subnets \
    --region us-east-1 \
    --filters Name=vpc-id,Values=<DEFAULT_VPC_ID>
```

This helped me understand which CIDR ranges were already being used.

I selected an available CIDR range within the default VPC CIDR block.

---

## Step 6: Create the Subnet

I created a subnet under the default VPC with the required name:

```text
devops-subnet
```

The subnet was created using the AWS CLI:

```bash
aws ec2 create-subnet \
    --vpc-id <DEFAULT_VPC_ID> \
    --cidr-block <AVAILABLE_CIDR_BLOCK> \
    --region us-east-1 \
    --tag-specifications 'ResourceType=subnet,Tags=[{Key=Name,Value=devops-subnet}]'
```

For example, if the selected available CIDR block was:

```text
172.31.32.0/20
```

the command would be:

```bash
aws ec2 create-subnet \
    --vpc-id <DEFAULT_VPC_ID> \
    --cidr-block 172.31.32.0/20 \
    --region us-east-1 \
    --tag-specifications 'ResourceType=subnet,Tags=[{Key=Name,Value=devops-subnet}]'
```

The CIDR block must be available within the default VPC and must not overlap with an existing subnet.

---

## Step 7: Verify the Created Subnet

After creating the subnet, I verified it using:

```bash
aws ec2 describe-subnets \
    --region us-east-1 \
    --filters Name=vpc-id,Values=<DEFAULT_VPC_ID>
```

This was an important requirement of the challenge because the verification had to apply the filter using:

```text
vpc-id
```

The command displays all subnets associated with the specified VPC.

---

## Step 8: Filter the Subnet by Name

I also verified the specific subnet using its `Name` tag:

```bash
aws ec2 describe-subnets \
    --region us-east-1 \
    --filters \
        Name=vpc-id,Values=<DEFAULT_VPC_ID> \
        Name=tag:Name,Values=devops-subnet
```

This confirmed that the subnet named:

```text
devops-subnet
```

was associated with the default VPC.

---

## Step 9: Display Important Subnet Information

To display only the important subnet details:

```bash
aws ec2 describe-subnets \
    --region us-east-1 \
    --filters Name=vpc-id,Values=<DEFAULT_VPC_ID> \
    --query 'Subnets[*].[SubnetId,VpcId,CidrBlock,AvailabilityZone,Tags[?Key==`Name`].Value|[0]]' \
    --output table
```

The output provides information such as:

```text
Subnet ID
VPC ID
CIDR Block
Availability Zone
Subnet Name
```

---

# Verification

The final subnet configuration was:

```text
Subnet Name : devops-subnet
VPC         : Default VPC
Region      : us-east-1
Status      : Successfully Created
```

The subnet was verified using `describe-subnets` with the required VPC ID filter:

```bash
aws ec2 describe-subnets \
    --region us-east-1 \
    --filters Name=vpc-id,Values=<DEFAULT_VPC_ID>
```

---

# AWS Networking Workflow

The overall workflow for this challenge was:

```text
AWS Credentials
       |
       v
showcreds
       |
       v
AWS CLI Authentication
       |
       v
Find Default VPC
       |
       v
Check Existing Subnets
       |
       v
Select Available CIDR
       |
       v
Create devops-subnet
       |
       v
describe-subnets
       |
       v
Filter using vpc-id
       |
       v
Subnet Verified
```

---

# What I Learned

From this challenge, I learned and practiced:

* How AWS VPCs provide network isolation.
* The relationship between VPCs and subnets.
* How to identify the default VPC.
* How to create a subnet inside a VPC.
* How CIDR blocks are used when creating subnets.
* How to check existing subnet ranges before creating a new subnet.
* How to add a `Name` tag to an AWS resource.
* How to use AWS CLI filters.
* How to verify subnets using `describe-subnets`.
* How to filter AWS subnets using a `vpc-id`.
* The importance of working in the correct AWS region.

# Challenge Status

Day 03 — Completed Successfully 

**Created the** **`devops-subnet`** **under the default VPC in the** **`us-east-1`** **region and verified the subnet using `describe-subnets` with the `vpc-id` filter.**

100 Days. 100 Challenges. One Cloud Journey. 

I will continue documenting each AWS challenge and building my practical cloud infrastructure skills step by step.
