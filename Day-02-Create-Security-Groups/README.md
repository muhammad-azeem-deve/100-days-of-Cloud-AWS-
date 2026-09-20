# Day 02 - Create AWS Security Group

## Challenge Overview

This is Day 02 of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The challenge was related to **AWS Security Groups, VPC Networking, and Inbound Traffic Rules**.

The Nautilus DevOps team is gradually migrating a portion of their infrastructure to the AWS cloud. To make the migration more manageable, the infrastructure is being divided into smaller tasks that can be implemented and verified step by step.

For this task, I was required to create a security group under the **default VPC** with specific inbound rules for HTTP and SSH traffic.

### Challenge Requirement

The requirements were:

1. Access the AWS Management Console using the KodeKloud lab environment.
2. Use the **us-east-1** AWS region.
3. Use the **default VPC**.
4. Create a security group named `xfusion-sg`.
5. Set the description to `Security group for Nautilus App Servers`.
6. Add an inbound **HTTP** rule for port `80`.
7. Allow HTTP traffic from `0.0.0.0/0`.
8. Add an inbound **SSH** rule for port `22`.
9. Allow SSH traffic from `0.0.0.0/0`.
10. Verify that the security group and its inbound rules were created successfully.

---

## Objectives

The objectives of this challenge are:

* Understand the purpose of AWS Security Groups.
* Work with the default VPC.
* Create a security group using AWS.
* Configure inbound network rules.
* Allow HTTP traffic on port 80.
* Allow SSH traffic on port 22.
* Understand CIDR notation.
* Verify AWS networking resources using the AWS CLI.
* Practice basic AWS cloud networking.

---

## Environment

| Item           | Details                                   |
| -------------- | ----------------------------------------- |
| Challenge      | 100 Days of Cloud (AWS)                   |
| Platform       | KodeKloud                                 |
| Day            | 02                                        |
| Cloud Provider | Amazon Web Services (AWS)                 |
| Service        | Amazon VPC / EC2                          |
| VPC            | Default VPC                               |
| Region         | `us-east-1`                               |
| Security Group | `xfusion-sg`                              |
| Description    | `Security group for Nautilus App Servers` |
| HTTP Port      | `80`                                      |
| SSH Port       | `22`                                      |
| Source CIDR    | `0.0.0.0/0`                               |
| Status         | Completed                                 |

---

# Solution

## Step 1: Access the AWS Lab Environment

First, I accessed the AWS Management Console using the temporary credentials provided by the KodeKloud lab.

The AWS region required for this challenge was:

```text
us-east-1
```

I made sure that all resources were created in the required region.

---

## Step 2: Open Amazon VPC

From the AWS Management Console, I opened the **VPC** service.

The navigation path was:

```text
AWS Management Console
        |
        v
       VPC
        |
        v
 Security Groups
```

---

## Step 3: Check the Default VPC

The task specifically required the security group to be created under the **default VPC**.

I opened the VPC dashboard and checked the available VPCs.

The required VPC was:

```text
Default VPC
```

I noted its VPC ID before creating the security group.

---

## Step 4: Open Security Groups

Inside the VPC dashboard, I navigated to:

```text
VPC
  -> Security Groups
```

I selected **Create security group**.

---

## Step 5: Configure the Security Group

I entered the following configuration:

```text
Security Group Name:
xfusion-sg

Description:
Security group for Nautilus App Servers
```

I selected the **default VPC** as the VPC for the security group.

The final basic configuration was:

```text
Name        : xfusion-sg
Description : Security group for Nautilus App Servers
VPC         : Default VPC
Region      : us-east-1
```

---

## Step 6: Add HTTP Inbound Rule

The first inbound rule was configured for HTTP traffic.

The rule was:

```text
Type        : HTTP
Protocol    : TCP
Port        : 80
Source      : 0.0.0.0/0
```

This allows HTTP traffic from all IPv4 addresses.

The CIDR range:

```text
0.0.0.0/0
```

represents all IPv4 addresses.

---

## Step 7: Add SSH Inbound Rule

The second inbound rule was configured for SSH traffic.

The rule was:

```text
Type        : SSH
Protocol    : TCP
Port        : 22
Source      : 0.0.0.0/0
```

This allows SSH traffic from all IPv4 addresses.

---

## Step 8: Create the Security Group

After configuring the security group and inbound rules, I selected **Create security group**.

AWS then created the security group under the default VPC.

---

## Step 9: Verify the Security Group

After creation, I checked the Security Groups section.

The security group was displayed with:

```text
Name:
xfusion-sg

Description:
Security group for Nautilus App Servers
```

I also verified that it belonged to the default VPC in:

```text
us-east-1
```

---

## Step 10: Verify Inbound Rules

The inbound rules were verified as follows:

| Type | Protocol | Port | Source      |
| ---- | -------- | ---- | ----------- |
| HTTP | TCP      | 80   | `0.0.0.0/0` |
| SSH  | TCP      | 22   | `0.0.0.0/0` |

Both required inbound rules were successfully configured.

---

# AWS CLI Verification

The security group can also be verified using the AWS CLI.

## Check AWS CLI

```bash
aws --version
```

---

## Verify AWS Identity

```bash
aws sts get-caller-identity
```

This verifies the AWS account and identity being used by the lab environment.

---

## Find the Default VPC

To find the default VPC in `us-east-1`:

```bash
aws ec2 describe-vpcs \
    --region us-east-1 \
    --filters Name=isDefault,Values=true
```

This returns information about the default VPC.

---

## Create the Security Group Using AWS CLI

The security group can be created with:

```bash
aws ec2 create-security-group \
    --group-name xfusion-sg \
    --description "Security group for Nautilus App Servers" \
    --vpc-id <default-vpc-id> \
    --region us-east-1
```

Replace:

```text
<default-vpc-id>
```

with the actual default VPC ID returned by AWS.

---

## Add HTTP Inbound Rule

The HTTP rule can be added using:

```bash
aws ec2 authorize-security-group-ingress \
    --group-name xfusion-sg \
    --protocol tcp \
    --port 80 \
    --cidr 0.0.0.0/0 \
    --region us-east-1
```

---

## Add SSH Inbound Rule

The SSH rule can be added using:

```bash
aws ec2 authorize-security-group-ingress \
    --group-name xfusion-sg \
    --protocol tcp \
    --port 22 \
    --cidr 0.0.0.0/0 \
    --region us-east-1
```

---

## Verify Security Group

To verify the security group:

```bash
aws ec2 describe-security-groups \
    --group-names xfusion-sg \
    --region us-east-1
```

---

## Verify Inbound Rules

To display the configured inbound rules:

```bash
aws ec2 describe-security-groups \
    --group-names xfusion-sg \
    --region us-east-1 \
    --query 'SecurityGroups[0].IpPermissions'
```

The output should contain rules for:

```text
Port 80 -> 0.0.0.0/0
Port 22 -> 0.0.0.0/0
```

---

# Security Group Configuration

The final configuration was:

```text
Security Group : xfusion-sg
Description    : Security group for Nautilus App Servers
VPC            : Default VPC
Region         : us-east-1
```

### Inbound Rules

```text
HTTP
Port        : 80
Protocol    : TCP
Source      : 0.0.0.0/0

SSH
Port        : 22
Protocol    : TCP
Source      : 0.0.0.0/0
```

---

# Security Group Workflow

The overall workflow for this challenge was:

```text
AWS Management Console
        |
        v
       VPC
        |
        v
   Default VPC
        |
        v
 Security Groups
        |
        v
 Create xfusion-sg
        |
        +----------------------+
        |                      |
        v                      v
     HTTP                    SSH
     Port 80                Port 22
        |                      |
        v                      v
  0.0.0.0/0              0.0.0.0/0
        |                      |
        +----------+-----------+
                   |
                   v
        Security Group Created
```

---

# What I Learned

From this challenge, I learned and practiced:

* How AWS Security Groups work.
* How Security Groups control inbound network traffic.
* How to work with the default VPC.
* How to create a Security Group.
* How to configure HTTP traffic on port 80.
* How to configure SSH traffic on port 22.
* How CIDR ranges such as `0.0.0.0/0` work.
* How to verify AWS networking resources.
* How to create and configure Security Groups using the AWS CLI.
* The importance of selecting the correct AWS region.

# Challenge Status

Day 02 — Completed Successfully 

**Created the** **`xfusion-sg`** **security group under the default VPC, configured HTTP port 80 and SSH port 22 inbound rules, and allowed traffic from** **`0.0.0.0/0`** **in the** **`us-east-1`** **region.**

100 Days. 100 Challenges. One Cloud Journey. 

I will continue documenting each challenge and building my practical AWS cloud skills step by step.
