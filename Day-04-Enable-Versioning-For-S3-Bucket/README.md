# Day 04 - Enable S3 Bucket Versioning

## Challenge Overview

This is Day 04 of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The challenge was related to **Amazon S3 Data Protection, Versioning, and Data Recovery**.

Data protection and recovery are important parts of cloud infrastructure. Accidental deletion or modification of files can result in data loss if there is no recovery mechanism in place.

The DevOps team received a requirement to enable versioning on an existing Amazon S3 bucket. S3 Versioning allows multiple versions of an object to be maintained, making it possible to recover previous versions of objects after accidental deletion or modification.

The S3 bucket provided for this task was:

```text
xfusion-s3-409051293
```

### Challenge Requirement

The requirements were:

1. Access the AWS environment using the KodeKloud lab credentials.
2. Use the **us-east-1** AWS region.
3. Locate the existing S3 bucket named `xfusion-s3-409051293`.
4. Enable **Versioning** for the bucket.
5. Verify that versioning was successfully enabled.

---

## Objectives

The objectives of this challenge are:

* Understand Amazon S3 bucket versioning.
* Learn how S3 protects objects from accidental deletion or modification.
* Access and manage an existing S3 bucket.
* Enable versioning on an S3 bucket.
* Verify S3 bucket configuration using the AWS Console.
* Verify bucket versioning using the AWS CLI.
* Understand basic data protection and recovery concepts in AWS.

---

## Environment

| Item           | Details                   |
| -------------- | ------------------------- |
| Challenge      | 100 Days of Cloud (AWS)   |
| Platform       | KodeKloud                 |
| Day            | 04                        |
| Cloud Provider | Amazon Web Services (AWS) |
| Service        | Amazon S3                 |
| Bucket         | `xfusion-s3-409051293`    |
| Region         | `us-east-1`               |
| Feature        | S3 Versioning             |
| Status         | Completed                 |

---

# Solution

## Step 1: Access the AWS Lab Environment

First, I accessed the AWS Management Console using the temporary credentials provided by the KodeKloud lab environment.

The credentials were valid only for the duration of the lab.

I did not include the credentials in this GitHub documentation because AWS credentials and passwords should never be published in a repository.

---

## Step 2: Select the Required AWS Region

The challenge specifically required the resource to be managed in:

```text
us-east-1
```

I selected:

```text
US East (N. Virginia) - us-east-1
```

from the AWS region selector.

---

## Step 3: Open Amazon S3

From the AWS Management Console, I opened the **Amazon S3** service.

The navigation was:

```text
AWS Management Console
        |
        v
     Amazon S3
        |
        v
      Buckets
```

---

## Step 4: Locate the S3 Bucket

Inside the S3 console, I searched for the required bucket:

```text
xfusion-s3-409051293
```

The bucket already existed, so I opened the bucket to manage its configuration.

---

## Step 5: Open Bucket Properties

After opening the bucket, I navigated to the **Properties** section.

The navigation was:

```text
S3
 |
 +-- Buckets
       |
       +-- xfusion-s3-409051293
              |
              +-- Properties
```

---

## Step 6: Enable Bucket Versioning

Inside the bucket properties, I located the **Bucket Versioning** section.

The current versioning status was checked first.

I enabled versioning for:

```text
xfusion-s3-409051293
```

The configuration was:

```text
Bucket:     xfusion-s3-409051293
Versioning: Enabled
Region:     us-east-1
```

After enabling versioning, I saved the configuration.

---

## Step 7: Verify Versioning

After saving the configuration, I checked the **Bucket Versioning** section again.

The status showed:

```text
Versioning: Enabled
```

This confirmed that versioning had been successfully enabled for the bucket.

---

# AWS CLI Verification

The same configuration can also be verified using the AWS CLI.

## Step 1: Verify AWS CLI

I first checked whether the AWS CLI was available:

```bash
aws --version
```

---

## Step 2: Retrieve Lab Credentials

The KodeKloud AWS client provides a `showcreds` command for retrieving the temporary lab credentials:

```bash
showcreds
```

The credentials should only be used within the lab environment.

> **Security Note:** Never copy AWS passwords, access keys, secret keys, or session tokens into GitHub repositories.

---

## Step 3: Verify AWS Identity

I verified the active AWS account using:

```bash
aws sts get-caller-identity
```

This confirms that the AWS CLI is connected to the lab account.

---

## Step 4: Check Current Versioning Status

Before enabling versioning, the current status of the S3 bucket can be checked using:

```bash
aws s3api get-bucket-versioning \
    --bucket xfusion-s3-409051293 \
    --region us-east-1
```

The command returns the bucket's versioning configuration.

---

## Step 5: Enable S3 Versioning

I enabled versioning for the required bucket using:

```bash
aws s3api put-bucket-versioning \
    --bucket xfusion-s3-409051293 \
    --versioning-configuration Status=Enabled \
    --region us-east-1
```

This command enables versioning for:

```text
Bucket: xfusion-s3-409051293
Region: us-east-1
```

---

## Step 6: Verify Versioning Status

After enabling versioning, I verified the configuration:

```bash
aws s3api get-bucket-versioning \
    --bucket xfusion-s3-409051293 \
    --region us-east-1
```

The expected result is:

```json
{
    "Status": "Enabled"
}
```

This confirms that S3 Versioning is enabled successfully.

---

# How S3 Versioning Protects Data

S3 Versioning keeps multiple versions of an object in the same bucket.

For example, if an object named:

```text
data.txt
```

is uploaded and later replaced, S3 can retain the previous version.

The basic workflow is:

```text
Upload data.txt
       |
       v
   Version 1
       |
       v
Update data.txt
       |
       v
   Version 2
       |
       v
Delete data.txt
       |
       v
Previous versions can still be retained
```

This helps protect against accidental overwrites and deletions.

---

# Verification

The final configuration was:

```text
S3 Bucket    : xfusion-s3-409051293
Region       : us-east-1
Versioning   : Enabled
Status       : Successfully Configured
```

---

# What I Learned

From this challenge, I learned and practiced:

* How Amazon S3 buckets are managed.
* How S3 Versioning works.
* How versioning helps protect objects from accidental deletion.
* How versioning helps retain previous versions of objects.
* How to enable S3 Versioning through the AWS Console.
* How to enable S3 Versioning using the AWS CLI.
* How to verify S3 bucket configuration.
* The importance of data protection and recovery in cloud infrastructure.
* The importance of using the correct AWS region when managing resources.

# Challenge Status

Day 04 — Completed Successfully 

**Enabled Versioning on the** **`xfusion-s3-409051293`** **S3 bucket in the** **`us-east-1`** **region and verified that the versioning status was set to** **`Enabled`**.

100 Days. 100 Challenges. One Cloud Journey. 

I will continue documenting each challenge and building my practical AWS cloud skills step by step.
