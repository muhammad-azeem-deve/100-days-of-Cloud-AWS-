# Day 39 - Hosting a Static Website on AWS S3

## Challenge Overview

This is Day 39 of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The challenge was related to **Amazon S3, Static Website Hosting, Bucket Permissions, and Public Access Configuration**.

The Nautilus DevOps team was tasked with creating an internal information portal that should be accessible to public users. To achieve this, the team decided to host a static website using Amazon S3.

The S3 bucket needed to be configured for static website hosting, public access, and website delivery through the S3 website endpoint.

For this challenge, I used the **AWS Management Console** to create and configure the S3 bucket, upload the website file, and verify that the website was publicly accessible.

### Challenge Requirement

The requirements were:

1. Access the AWS Management Console.
2. Create an S3 bucket named `<your-bucket-name>`.
3. Use the `us-east-1` AWS region.
4. Disable Block Public Access settings for the bucket.
5. Enable static website hosting.
6. Configure `index.html` as the index document.
7. Configure a bucket policy to allow public read access.
8. Upload the `index.html` file from the `/root/` directory of the AWS client host.
9. Verify that the uploaded file is available in the S3 bucket.
10. Access the website using the S3 website endpoint.

---

## Objectives

The objectives of this challenge are:

- Understand Amazon S3 and object storage.
- Create and configure an S3 bucket.
- Understand S3 bucket permissions.
- Configure public access to an S3 bucket.
- Enable static website hosting.
- Configure an index document.
- Upload website files to Amazon S3.
- Create a bucket policy for public read access.
- Understand S3 website endpoints.
- Verify website accessibility through a browser and AWS CLI.

---

## Environment

| Item | Details |
| ----------------- | ------------------------------ |
| Challenge | 100 Days of Cloud (AWS) |
| Platform | KodeKloud |
| Day | 39 |
| Cloud Provider | Amazon Web Services (AWS) |
| AWS Service | Amazon S3 |
| Region | `us-east-1` |
| Bucket Name | `<your-bucket-name>` |
| Website File | `index.html` |
| Source Directory | `/root/` |
| Index Document | `index.html` |
| Hosting Type | Static Website Hosting |
| Access | Public Read |
| Status | Completed |

---

# Solution

## Step 1: Access the AWS Management Console

First, I logged into the AWS Management Console using the temporary credentials provided by the KodeKloud lab environment.

After successfully logging in, I selected the required AWS region:

```text
us-east-1
```

All resources for this challenge were created in the specified region.

---

## Step 2: Open Amazon S3

From the AWS Management Console, I searched for and opened the **Amazon S3** service.

The navigation path was:

```text
AWS Management Console
        |
        v
      Amazon S3
        |
        v
    Create Bucket
```

Amazon S3 is an object storage service that can also host static websites.

---

## Step 3: Create an S3 Bucket

I selected **Create bucket** and configured the bucket with the required settings.

The bucket configuration was:

```text
Bucket Name: <your-bucket-name>
AWS Region: us-east-1
```

Under the **Block Public Access settings for this bucket** section, I unchecked:

```text
Block all public access
```

AWS displayed a warning about making the bucket publicly accessible.

I acknowledged the warning because public access was required for this challenge.

I left the other settings at their default values and selected **Create bucket**.

---

## Step 4: Verify the S3 Bucket

After creating the bucket, I returned to the S3 bucket list.

I verified that the bucket appeared with the correct name and region.

Expected configuration:

```text
Bucket Name: <your-bucket-name>
Region: us-east-1
Status: Created
```

---

## Step 5: Open the Bucket Properties

I opened the newly created S3 bucket.

Inside the bucket, I selected the **Properties** tab.

I scrolled down to the:

```text
Static website hosting
```

section.

By default, static website hosting is disabled for a newly created bucket.

---

## Step 6: Enable Static Website Hosting

I selected **Edit** under the Static website hosting section.

I configured the following settings:

```text
Static website hosting: Enable

Hosting type: Host a static website

Index document: index.html
```

The index document is the default page that AWS S3 serves when a user accesses the website endpoint.

After configuring these settings, I selected **Save changes**.

Static website hosting was now enabled for the bucket.

---

## Step 7: Configure the Bucket Policy

After enabling static website hosting, I opened the **Permissions** tab.

I scrolled down to the **Bucket policy** section and selected **Edit**.

I added the following bucket policy, replacing the placeholder with my actual bucket name:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::<your-bucket-name>/*"
        }
    ]
}
```

### Understanding the Bucket Policy

| Policy Element | Purpose |
| ---------------- | ------------------------------ |
| Version | Defines the policy language version |
| Effect | Allows the requested action |
| Principal | `*` means everyone |
| Action | `s3:GetObject` allows reading objects |
| Resource | Applies permission to all objects in the bucket |

This policy allows public users to read objects stored inside the S3 bucket.

After adding the policy, I selected **Save changes**.

---

## Step 8: Upload the index.html File

The required website file was located on the AWS client host at:

```text
/root/index.html
```

I accessed the file from the AWS client host and made it available for upload through the AWS Management Console.

I then followed these steps:

1. Opened the S3 bucket.
2. Selected the **Objects** tab.
3. Clicked **Upload**.
4. Selected **Add files**.
5. Selected the `index.html` file.
6. Reviewed the upload settings.
7. Selected **Upload**.

The file was uploaded successfully to the S3 bucket.

The bucket now contained:

```text
<your-bucket-name>/
    |
    └── index.html
```

---

## Step 9: Verify the Uploaded File

After uploading the file, I returned to the **Objects** tab.

I verified that the following object was available:

```text
index.html
```

I also checked that the upload status was successful.

This confirmed that the website's main HTML file was stored in the S3 bucket.

---

## Step 10: Verify Static Website Hosting

I returned to the **Properties** tab and scrolled down to the Static website hosting section.

I verified the following configuration:

```text
Static website hosting: Enabled

Index document: index.html
```

AWS also displayed the **Bucket website endpoint**.

I copied this endpoint to access the website.

---

## Step 11: Access the Static Website

I opened the Bucket website endpoint in a web browser.

The endpoint follows this format:

```text
http://<your-bucket-name>.s3-website-us-east-1.amazonaws.com
```

The website loaded successfully.

The expected page displayed:

```text
Welcome to KKE labs!
```

This confirmed that the S3 bucket was serving the uploaded HTML file through its static website endpoint.

---

## Step 12: Final Verification

Finally, I verified the following configurations through the AWS Management Console:

| Verification | Expected Result |
| ------------------------- | ---------------- |
| S3 Bucket | Created |
| Bucket Region | `us-east-1` |
| Block Public Access | Disabled |
| Static Website Hosting | Enabled |
| Index Document | `index.html` |
| Bucket Policy | Public Read |
| Website File | Uploaded |
| Website Endpoint | Accessible |

I also verified the bucket configuration and uploaded object using AWS CLI verification commands.

---

# AWS S3 Static Website Workflow

The overall workflow for this challenge was:

```text
AWS Management Console
        |
        v
    Amazon S3
        |
        v
    Create Bucket
        |
        v
  Disable Block Public Access
        |
        v
 Enable Static Website Hosting
        |
        v
 Configure index.html
        |
        v
 Configure Bucket Policy
        |
        v
    Upload index.html
        |
        v
  Verify Uploaded Object
        |
        v
  Open Website Endpoint
        |
        v
  Static Website Accessible
```

---

# What I Learned

From this challenge, I learned and practiced:

- How to create an S3 bucket using the AWS Management Console.
- How to configure an AWS region.
- How to manage Block Public Access settings.
- How to enable static website hosting on Amazon S3.
- How to configure an index document.
- How to upload website files to an S3 bucket.
- How to create an S3 bucket policy.
- How to provide public read access to S3 objects.
- How to access a website using an S3 website endpoint.
- How to verify S3 resources using the AWS Console and AWS CLI.
- The difference between S3 object storage and static website hosting.

---

# Important Security Notes

Public access should only be enabled when the website content is intentionally meant to be publicly accessible.

The bucket policy used in this challenge allows everyone to read objects in the bucket.

For production environments, access permissions should be carefully reviewed. A private S3 bucket combined with Amazon CloudFront is another common approach to hosting static websites.

---

# Challenge Status

Day 39 — Completed Successfully ✅

**Created an Amazon S3 bucket, configured public access, enabled static website hosting, uploaded `index.html`, configured the bucket policy, and verified that the website was accessible through the S3 website endpoint.**

100 Days. 100 Challenges. One Cloud Journey. ☁️🚀

I will continue documenting each challenge and building my practical AWS cloud skills step by step.
