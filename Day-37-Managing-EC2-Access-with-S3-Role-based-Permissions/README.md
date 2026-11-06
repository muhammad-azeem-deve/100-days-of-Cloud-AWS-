# Day 37 - Managing EC2 Access with S3 Role-based Permissions

## Challenge Overview

This is Day 37 of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The challenge was related to **AWS IAM Roles, IAM Policies, Amazon EC2, and Amazon S3 Bucket Access Management**.

The Nautilus DevOps team needed to configure an existing EC2 instance to interact securely with an Amazon S3 bucket. The objective was to allow the EC2 instance to upload, download, and list objects in a private S3 bucket without storing AWS access keys directly on the instance.

To achieve this, I created a private S3 bucket, configured an IAM policy with the required permissions, created an IAM role for EC2, and attached that role to the existing EC2 instance.

### Challenge Requirement

The requirements were:

1. Access the AWS Management Console.
2. Locate the existing EC2 instance named `xfusion-ec2`.
3. Configure SSH access to the EC2 instance using a new SSH key pair generated on the AWS client.
4. Create a private S3 bucket named `xfusion-s3-29734`.
5. Create an IAM policy named `xfusion-s3-policy`.
6. Allow the following S3 permissions:
   - `s3:PutObject`
   - `s3:GetObject`
   - `s3:ListBucket`
7. Create an IAM role named `xfusion-role`.
8. Configure EC2 as the trusted entity for the IAM role.
9. Attach the custom IAM policy to the role.
10. Attach the IAM role to the existing `xfusion-ec2` instance.
11. Verify that the EC2 instance can upload and list objects in the S3 bucket.

---

## Objectives

The objectives of this challenge are:

- Understand AWS IAM roles and policies.
- Learn how EC2 instances access other AWS services.
- Create and configure a private S3 bucket.
- Understand S3 bucket-level and object-level permissions.
- Create a custom IAM policy using JSON.
- Configure an IAM role with EC2 as its trusted entity.
- Attach an IAM role to an existing EC2 instance.
- Configure SSH access to an EC2 instance.
- Upload and retrieve files from S3 using EC2.
- Understand role-based authentication without storing permanent AWS credentials.

---

## Environment

| Item | Details |
| ----------------- | ------------------------------ |
| Challenge | 100 Days of Cloud (AWS) |
| Platform | KodeKloud |
| Day | 37 |
| Cloud Provider | Amazon Web Services (AWS) |
| Services | EC2, S3, IAM |
| EC2 Instance | `xfusion-ec2` |
| S3 Bucket | `xfusion-s3-29734` |
| Bucket Access | Private |
| IAM Policy | `xfusion-s3-policy` |
| IAM Role | `xfusion-role` |
| S3 Permissions | PutObject, GetObject, ListBucket |
| Configuration Method | AWS Management Console |
| Verification Method | AWS Console and AWS CLI |
| Status | Completed |

---

# Solution

## Step 1: Access the AWS Management Console

First, I logged into the AWS Management Console using the temporary credentials provided by the KodeKloud lab environment.

I made sure to select the required AWS region:

```text
us-east-1
```

The same region was used for the EC2 and S3 resources in this lab.

---

## Step 2: Locate the Existing EC2 Instance

I opened the Amazon EC2 service and navigated to:

```text
EC2
  |
  v
Instances
```

I located the existing instance:

```text
Instance Name: xfusion-ec2
```

I checked that the instance was running and noted its public IP address.

This existing instance would be used to access the S3 bucket through an IAM role.

---

## Step 3: Generate an SSH Key Pair

To establish SSH access from the AWS client machine, I generated a new RSA key pair.

I opened the AWS client terminal and executed:

```bash
ssh-keygen -t rsa -f /root/.ssh/id_rsa -N ""
```

This generated two files:

```text
/root/.ssh/id_rsa
/root/.ssh/id_rsa.pub
```

- `id_rsa` is the private key.
- `id_rsa.pub` is the public key.

I copied the public key content for the next step.

**Security Note:** The private key must remain secure and should never be uploaded to GitHub.

---

## Step 4: Configure SSH Access Through EC2 Connect

I returned to the AWS Console and opened the existing `xfusion-ec2` instance.

I selected:

```text
EC2
  |
  v
Instances
  |
  v
xfusion-ec2
  |
  v
Connect
  |
  v
EC2 Instance Connect
```

I connected using the available EC2 Instance Connect option.

After connecting, I obtained root access:

```bash
sudo -i
```

I opened the authorized keys file:

```bash
vi /root/.ssh/authorized_keys
```

I pasted the public key generated in Step 3 into this file and saved the changes.

This configured passwordless SSH authentication using the generated key pair.

---

## Step 5: Create a Private S3 Bucket

Next, I opened the Amazon S3 service from the AWS Management Console.

Navigation:

```text
AWS Console
  |
  v
S3
  |
  v
Buckets
  |
  v
Create bucket
```

I configured the bucket using the following details:

```text
Bucket Name: xfusion-s3-29734
Region: us-east-1
```

For security, I kept all Block Public Access settings enabled.

I also kept the default ACL-disabled Object Ownership configuration.

Finally, I selected **Create bucket**.

The private S3 bucket was created successfully.

---

## Step 6: Create an IAM Policy

Next, I created a custom IAM policy to provide the EC2 instance with the required S3 permissions.

I opened the IAM service and navigated to:

```text
IAM
  |
  v
Policies
  |
  v
Create policy
```

I selected the **JSON** tab and added the following policy:

### IAM Policy JSON

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:PutObject",
                "s3:GetObject"
            ],
            "Resource": "arn:aws:s3:::xfusion-s3-29734/*"
        },
        {
            "Effect": "Allow",
            "Action": "s3:ListBucket",
            "Resource": "arn:aws:s3:::xfusion-s3-29734"
        }
    ]
}
```

### Policy Explanation

The policy contains three important S3 permissions:

| Permission | Purpose |
| ---------------- | ------------------------------ |
| `s3:PutObject` | Allows uploading objects to S3 |
| `s3:GetObject` | Allows downloading objects from S3 |
| `s3:ListBucket` | Allows listing objects in the bucket |

The resource ARN is different for bucket-level and object-level permissions:

```text
Bucket ARN:
arn:aws:s3:::xfusion-s3-29734

Object ARN:
arn:aws:s3:::xfusion-s3-29734/*
```

I selected **Next** and provided the policy name:

```text
xfusion-s3-policy
```

Finally, I selected **Create policy**.

The custom IAM policy was created successfully.

---

## Step 7: Create an IAM Role for EC2

After creating the policy, I created an IAM role that EC2 could assume.

I navigated to:

```text
IAM
  |
  v
Roles
  |
  v
Create role
```

I configured the trusted entity as follows:

```text
Trusted Entity Type: AWS Service
Use Case: EC2
```

I selected **EC2** because the role would be attached to an EC2 instance.

I selected **Next**.

---

## Step 8: Attach the IAM Policy to the Role

On the permissions page, I searched for the custom policy:

```text
xfusion-s3-policy
```

I selected the policy checkbox and continued.

This attached the required S3 permissions to the IAM role.

---

## Step 9: Name and Create the IAM Role

I provided the following role name:

```text
Role Name: xfusion-role
```

I reviewed the trusted entity and attached permissions.

The final configuration was:

```text
Role Name: xfusion-role

Trusted Entity: EC2

Attached Policy: xfusion-s3-policy
```

Finally, I selected **Create role**.

The IAM role was created successfully.

---

## Step 10: Attach the IAM Role to the EC2 Instance

Next, I attached the newly created IAM role to the existing EC2 instance.

I navigated to:

```text
EC2
  |
  v
Instances
  |
  v
Select xfusion-ec2
  |
  v
Actions
  |
  v
Security
  |
  v
Modify IAM role
```

I selected the following IAM role:

```text
xfusion-role
```

Then I selected **Update IAM role**.

The IAM role was successfully attached to the EC2 instance.

Now, the EC2 instance could obtain temporary AWS credentials through its attached IAM role.

---

## Step 11: Connect to the EC2 Instance

I returned to the AWS client terminal and connected to the EC2 instance using its public IP address and the private SSH key.

The command format was:

```bash
ssh -i /root/.ssh/id_rsa root@<EC2-PUBLIC-IP>
```

After successfully connecting, I was inside the EC2 instance.

---

## Step 12: Create a Test File

To test the S3 permissions, I created a sample file inside the EC2 instance:

```bash
echo "S3 access test" > testfile.txt
```

This file was used to verify the upload permission.

---

## Step 13: Upload the File to S3

I uploaded the test file to the private S3 bucket:

```bash
aws s3 cp testfile.txt s3://xfusion-s3-29734/
```

The command used the IAM role attached to the EC2 instance.

No permanent AWS access keys were required.

A successful upload confirmed that the instance had the required `s3:PutObject` permission.

---

## Step 14: List Objects in the S3 Bucket

I executed the following command from the EC2 instance:

```bash
aws s3 ls s3://xfusion-s3-29734/
```

The uploaded file appeared in the bucket listing:

```text
testfile.txt
```

This confirmed that the EC2 instance had the required `s3:ListBucket` permission.

---

## Step 15: Verify the Uploaded Object in AWS Console

Finally, I returned to the AWS Management Console.

I navigated to:

```text
S3
  |
  v
Buckets
  |
  v
xfusion-s3-29734
  |
  v
Objects
```

I verified that the following object was present:

```text
testfile.txt
```

The file appeared successfully in the private S3 bucket.

---

# AWS Resource Configuration

The final resource configuration was:

| Resource | Name | Configuration |
| ---------------- | ---------------- | ---------------- |
| EC2 Instance | `xfusion-ec2` | Existing instance |
| S3 Bucket | `xfusion-s3-29734` | Private |
| IAM Policy | `xfusion-s3-policy` | S3 permissions |
| IAM Role | `xfusion-role` | EC2 trusted entity |
| Attached Policy | `xfusion-s3-policy` | Attached to role |
| Test Object | `testfile.txt` | Uploaded successfully |

---

# Architecture Workflow

The overall architecture for this challenge was:

```text
          AWS Client
              |
              | SSH
              v
       EC2 Instance
        xfusion-ec2
              |
              | Assumes
              v
          IAM Role
        xfusion-role
              |
              | Attached Policy
              v
        IAM Policy
    xfusion-s3-policy
              |
              | Allows
              v
        Amazon S3
    xfusion-s3-29734
              |
              v
         testfile.txt
```

---

# Security Best Practices

This challenge demonstrated an important AWS security practice.

Instead of storing permanent AWS access keys directly on an EC2 instance, I used an IAM role.

Important security practices:

- Use IAM roles for communication between AWS services.
- Follow the principle of least privilege.
- Keep S3 buckets private unless public access is specifically required.
- Never store permanent AWS credentials on EC2 instances.
- Never upload SSH private keys to GitHub.
- Grant only the required S3 permissions.
- Keep Block Public Access enabled for private buckets.

---

# What I Learned

From this challenge, I learned and practiced:

- How to create and configure a private S3 bucket.
- How to generate SSH key pairs.
- How to configure passwordless SSH authentication.
- How to create custom IAM policies using JSON.
- How to configure bucket-level and object-level S3 permissions.
- How to create an IAM role for EC2.
- How to attach an IAM policy to an IAM role.
- How to attach an IAM role to an existing EC2 instance.
- How EC2 uses temporary credentials through IAM roles.
- How to upload files from EC2 to S3.
- How to verify S3 access using the AWS CLI and Management Console.
- Why IAM roles are safer than storing permanent AWS credentials.

# Challenge Status

Day 37 — Completed Successfully ✅

**Created the private S3 bucket `xfusion-s3-29734`, configured the IAM policy `xfusion-s3-policy`, created the EC2 IAM role `xfusion-role`, attached it to the existing `xfusion-ec2` instance, and successfully uploaded and verified `testfile.txt` in the S3 bucket.**

100 Days. 100 Challenges. One Cloud Journey. ☁️🚀

I will continue documenting each challenge and building my practical AWS cloud skills step by step.
