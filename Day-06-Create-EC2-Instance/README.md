# Task 06 - Create an EC2 Instance

## Challenge Overview

This is **Task 06** of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The challenge was related to **Amazon EC2 Instance Creation, AMI Selection, Instance Types, Key Pairs, and Security Groups**.

The Nautilus DevOps team is gradually migrating its infrastructure to the AWS cloud. Instead of performing the migration as one large operation, the team is breaking the migration into smaller tasks that can be completed and verified independently.

For this task, I was required to create an EC2 instance with a specific name, Amazon Linux AMI, instance type, RSA key pair, and the default security group.

### Challenge Requirement

The requirements were:

1. Create an EC2 instance in the `us-east-1` region.
2. The instance name must be `xfusion-ec2`.
3. Use an **Amazon Linux AMI**.
4. Use the `t2.micro` instance type.
5. Create a new RSA key pair named `xfusion-kp`.
6. Attach the default available security group.
7. Verify that the EC2 instance was created successfully.

---

## Objectives

The objectives of this challenge are:

* Understand the basics of Amazon EC2.
* Launch an EC2 instance using the AWS Management Console.
* Select an appropriate Amazon Machine Image (AMI).
* Understand EC2 instance types.
* Create and use an RSA key pair.
* Understand the purpose of security groups.
* Use the default security group with an EC2 instance.
* Verify the running EC2 instance.
* Practice AWS CLI commands for EC2 management.

---

## Environment

| Item           | Details                   |
| -------------- | ------------------------- |
| Challenge      | 100 Days of Cloud (AWS)   |
| Platform       | KodeKloud                 |
| Day            | 06                        |
| Cloud Provider | Amazon Web Services (AWS) |
| Service        | Amazon EC2                |
| Region         | `us-east-1`               |
| Instance Name  | `xfusion-ec2`             |
| AMI            | Amazon Linux              |
| Instance Type  | `t2.micro`                |
| Key Pair       | `xfusion-kp`              |
| Key Pair Type  | RSA                       |
| Security Group | Default Security Group    |
| Status         | Completed                 |

---

# Solution

## Step 1: Access the AWS Lab Environment

First, I accessed the AWS Management Console using the temporary credentials provided by the KodeKloud lab environment.

The lab credentials were used only for the duration of the practical lab.

> **Security Note:** AWS credentials were not added to this GitHub repository.

---

## Step 2: Select the Required AWS Region

The challenge required the EC2 instance to be created in:

```text
us-east-1
```

I selected:

```text
US East (N. Virginia) - us-east-1
```

from the AWS region selector.

---

## Step 3: Open Amazon EC2

From the AWS Management Console, I opened the **EC2** service.

The navigation was:

```text
AWS Management Console
        |
        v
       EC2
        |
        v
Launch Instance
```

---

## Step 4: Configure the Instance Name

During instance creation, I entered the required instance name:

```text
xfusion-ec2
```

The name is stored as an AWS `Name` tag.

---

## Step 5: Select the Amazon Linux AMI

For the Amazon Machine Image, I selected an **Amazon Linux** AMI.

The AMI provides the operating system and software configuration used to launch the EC2 instance.

The selected operating system was:

```text
Amazon Linux
```

---

## Step 6: Select the Instance Type

The required instance type was:

```text
t2.micro
```

The instance type determines the compute resources allocated to the EC2 instance.

The configuration was:

```text
Instance Type: t2.micro
```

---

## Step 7: Create the RSA Key Pair

The challenge required a new key pair.

I selected:

```text
Create new key pair
```

and configured it as:

```text
Key Pair Name: xfusion-kp
Key Pair Type: RSA
```

The private key was downloaded after creating the key pair.

The private key should be stored securely and must not be uploaded to GitHub.

---

## Step 8: Select the Default Security Group

For network security, the challenge required the default available security group.

I selected the existing:

```text
default
```

security group.

A security group acts as a virtual firewall for an EC2 instance and controls inbound and outbound traffic.

---

## Step 9: Launch the EC2 Instance

After checking the configuration, I launched the instance.

The final configuration was:

```text
Name          : xfusion-ec2
AMI           : Amazon Linux
Instance Type : t2.micro
Key Pair      : xfusion-kp
Key Type      : RSA
Security Group: default
Region        : us-east-1
```

I selected **Launch Instance**.

---

## Step 10: Verify the EC2 Instance

After launching the instance, I opened:

```text
EC2
  -> Instances
  -> Instances
```

The instance appeared in the EC2 instance list.

I verified:

```text
Instance Name : xfusion-ec2
Instance Type : t2.micro
State         : Running
Region        : us-east-1
```

The instance was successfully created.

---

# AWS CLI Verification

The same EC2 instance can also be verified using the AWS CLI.

## Step 1: Verify AWS Identity

```bash
aws sts get-caller-identity
```

This verifies the AWS account and identity associated with the current credentials.

---

## Step 2: Find the Amazon Linux AMI

The exact AMI ID can vary by AWS region and Amazon Linux version, so it is better to query AWS rather than hard-code an AMI ID.

For example:

```bash
aws ec2 describe-images \
    --owners amazon \
    --region us-east-1 \
    --filters "Name=name,Values=al2023-ami-*" \
    --query 'Images | sort_by(@, &CreationDate)[-1].[ImageId,Name]' \
    --output table
```

This can be used to identify a current Amazon Linux AMI available in `us-east-1`.

---

## Step 3: Find the Default Security Group

The default security group can be identified using:

```bash
aws ec2 describe-security-groups \
    --filters Name=group-name,Values=default \
    --region us-east-1 \
    --query 'SecurityGroups[*].[GroupId,GroupName,VpcId]' \
    --output table
```

The output provides the default security group's ID.

---

## Step 4: Create the RSA Key Pair

The required key pair can be created using:

```bash
aws ec2 create-key-pair \
    --key-name xfusion-kp \
    --key-type rsa \
    --region us-east-1 \
    --query 'KeyMaterial' \
    --output text > xfusion-kp.pem
```

The private key should then be protected:

```bash
chmod 400 xfusion-kp.pem
```

---

## Step 5: Launch the EC2 Instance

After obtaining the correct Amazon Linux AMI ID and default security group ID, the instance can be launched using:

```bash
aws ec2 run-instances \
    --image-id <AMAZON-LINUX-AMI-ID> \
    --instance-type t2.micro \
    --key-name xfusion-kp \
    --security-group-ids <DEFAULT-SECURITY-GROUP-ID> \
    --region us-east-1 \
    --tag-specifications 'ResourceType=instance,Tags=[{Key=Name,Value=xfusion-ec2}]'
```

The placeholders should be replaced with the actual values returned by AWS.

---

## Step 6: Find the Created Instance

The instance can be located using its `Name` tag:

```bash
aws ec2 describe-instances \
    --filters "Name=tag:Name,Values=xfusion-ec2" \
    --region us-east-1 \
    --query 'Reservations[*].Instances[*].[InstanceId,InstanceType,State.Name,PrivateIpAddress]' \
    --output table
```

---

## Step 7: Verify the Instance Configuration

The instance configuration can be checked with:

```bash
aws ec2 describe-instances \
    --filters "Name=tag:Name,Values=xfusion-ec2" \
    --region us-east-1 \
    --query 'Reservations[*].Instances[*].[InstanceId,InstanceType,State.Name,KeyName]' \
    --output table
```

Expected information:

```text
xfusion-ec2
t2.micro
running
xfusion-kp
```

---

# EC2 Configuration

The final EC2 configuration was:

```text
Instance Name : xfusion-ec2
AMI           : Amazon Linux
Instance Type : t2.micro
Key Pair      : xfusion-kp
Key Type      : RSA
Security Group: default
Region        : us-east-1
Status        : Running
```

---

# EC2 Launch Workflow

The overall workflow for this challenge was:

```text
AWS Management Console
        |
        v
       EC2
        |
        v
 Launch Instance
        |
        +----------------------+
        |                      |
        v                      v
 Amazon Linux             t2.micro
        |                      |
        +----------+-----------+
                   |
                   v
             xfusion-kp
                RSA
                   |
                   v
           Default Security
                Group
                   |
                   v
             xfusion-ec2
                   |
                   v
               Running
```

---

# Security Notes

The private key generated during this task is sensitive.

I did not include the private key or AWS credentials in this repository.

The following should **never** be committed to GitHub:

```text
xfusion-kp.pem
*.pem
AWS Access Key ID
AWS Secret Access Key
AWS Session Token
AWS Console Password
```

If the private key is stored inside the project directory, add it to `.gitignore`:

```gitignore
*.pem
*.key
.env
```

---

# What I Learned

From this challenge, I learned and practiced:

* How to launch an EC2 instance.
* How Amazon Machine Images are used to create EC2 instances.
* How to select an Amazon Linux AMI.
* How EC2 instance types work.
* How to use the `t2.micro` instance type.
* How to create an RSA key pair.
* How key pairs are used for secure EC2 access.
* How security groups work as virtual firewalls.
* How to use the default security group.
* How to verify EC2 instances using the AWS Console.
* How to manage and verify EC2 resources using AWS CLI.
* How AWS regions affect resource creation.

# Challenge Status

Task 06 — Completed Successfully 

**Created the** **`xfusion-ec2`** **EC2 instance using an Amazon Linux AMI, `t2.micro` instance type, RSA key pair `xfusion-kp`, and the default security group in the** **`us-east-1`** **region.**

100 Days. 100 Challenges. One Cloud Journey. 

I will continue documenting each challenge and building my practical AWS cloud skills step by step.
