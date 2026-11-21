# Day 46 - Event-Driven Processing with Amazon S3 and Lambda

## Challenge Overview

This is Day 46 of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The challenge was related to **Event-Driven Architecture, Amazon S3, AWS Lambda, DynamoDB, and IAM Roles**.

The Nautilus DevOps team wanted to automate secure file handling between two Amazon S3 buckets. Whenever a file was uploaded to a public S3 bucket, an AWS Lambda function had to automatically copy that file into a private S3 bucket.

Additionally, the Lambda function had to record the file transfer details in a DynamoDB table to maintain an execution log.

The entire infrastructure was configured in the **us-east-1** AWS region.

### Challenge Requirement

The requirements were:

1. Create a public S3 bucket named `nautilus-public-221033535`.
2. Configure the public bucket to allow public read access.
3. Create a private S3 bucket named `nautilus-private-221033535`.
4. Block all public access to the private bucket.
5. Create a DynamoDB table named `nautilus-S3CopyLogs`.
6. Configure `LogID` as the DynamoDB partition key.
7. Create an IAM role named `lambda_execution_role`.
8. Attach the required permissions for S3, DynamoDB, and CloudWatch Logs.
9. Create a Lambda function named `nautilus-copyfunction`.
10. Configure the Lambda function with 128 MB memory and a 10-second timeout.
11. Use the provided Lambda script and configure the private bucket and DynamoDB table names.
12. Configure an S3 event notification for `s3:ObjectCreated:*`.
13. Automatically invoke the Lambda function whenever a file is uploaded to the public bucket.
14. Verify that uploaded files are copied to the private bucket and their execution details are recorded in DynamoDB.

---

## Objectives

The objectives of this challenge are:

- Understand event-driven architecture in AWS.
- Create and configure Amazon S3 buckets.
- Understand public and private S3 bucket configurations.
- Configure S3 bucket permissions and public access settings.
- Create a DynamoDB table.
- Understand DynamoDB partition keys.
- Create IAM roles and attach permissions.
- Configure AWS Lambda functions.
- Integrate Amazon S3 with AWS Lambda.
- Automate file processing using S3 event notifications.
- Store execution logs in DynamoDB.
- Test and verify automated file transfers.

---

## Environment

| Item | Details |
| ---------------------- | -------------------------------- |
| Challenge | 100 Days of Cloud (AWS) |
| Platform | KodeKloud |
| Day | 46 |
| Cloud Provider | AWS |
| Region | `us-east-1` |
| Public S3 Bucket | `nautilus-public-221033535` |
| Private S3 Bucket | `nautilus-private-221033535` |
| DynamoDB Table | `nautilus-S3CopyLogs` |
| Partition Key | `LogID` |
| IAM Role | `lambda_execution_role` |
| Lambda Function | `nautilus-copyfunction` |
| Lambda Memory | 128 MB |
| Lambda Timeout | 10 seconds |
| S3 Event | `s3:ObjectCreated:*` |
| Test File | `sample.zip` |
| Final Status | Completed Successfully |

---

# Solution

## Step 1: Access the AWS Management Console

First, I accessed the AWS Management Console using the temporary credentials provided by the KodeKloud lab environment.

I selected the required AWS region:

```text
us-east-1
```

All resources for this challenge were created in the same region.

---

## Step 2: Create the Public S3 Bucket

I opened the Amazon S3 service and selected **Create bucket**.

I created the public bucket with the following configuration:

```text
Bucket Name: nautilus-public-221033535
Region: us-east-1
```

The bucket was configured to allow public read access for the required upload workflow.

I reviewed the Block Public Access settings and configured the necessary permissions for public access.

The bucket policy was configured to allow public read access to objects.

---

## Step 3: Create the Private S3 Bucket

Next, I created another S3 bucket to store the copied files securely.

The configuration was:

```text
Bucket Name: nautilus-private-221033535
Region: us-east-1
```

For this bucket, I kept **Block all public access** enabled.

This ensured that the destination bucket remained private and that uploaded files could not be accessed publicly.

---

## Step 4: Create the DynamoDB Table

I opened the Amazon DynamoDB service and selected **Create table**.

I configured the table using the following details:

```text
Table Name: nautilus-S3CopyLogs
Partition Key: LogID
Partition Key Type: String
Region: us-east-1
```

The table was created successfully.

This table was used to store information about each file-copy operation performed by the Lambda function.

The execution logs included:

- Source bucket name.
- Destination bucket name.
- Uploaded object key.
- Execution status.

---

## Step 5: Create the IAM Execution Role

Next, I opened the IAM service and created an IAM role for the Lambda function.

The role name was:

```text
lambda_execution_role
```

I configured the trusted entity as:

```text
AWS Service: Lambda
```

This allowed the Lambda service to assume the role during function execution.

I attached the required permissions covering:

1. Amazon S3 - To read objects from the public bucket and write objects to the private bucket.
2. Amazon DynamoDB - To insert file-copy execution records into the DynamoDB table.
3. CloudWatch Logs - To create log groups and write execution logs.

The IAM role was created successfully and was used by the Lambda function.

---

## Step 6: Create the AWS Lambda Function

I opened the AWS Lambda service and selected **Create function**.

I configured the function with the following details:

```text
Function Name: nautilus-copyfunction
Runtime: As Provided by the Lab
Architecture: As Provided by the Lab
Execution Role: lambda_execution_role
```

The Lambda function was created successfully.

---

## Step 7: Configure Lambda Memory and Timeout

After creating the function, I opened its configuration settings.

I updated the memory and timeout values:

```text
Memory: 128 MB
Timeout: 10 seconds
```

These settings were configured according to the challenge requirements.

---

## Step 8: Configure the Lambda Function Code

I opened the Lambda function's code editor and used the script provided by the KodeKloud lab.

I updated the required configuration values in the script.

The destination S3 bucket name was:

```text
nautilus-private-221033535
```

The DynamoDB table name was:

```text
nautilus-S3CopyLogs
```

The Lambda function was configured to:

1. Receive an S3 object-created event.
2. Identify the source bucket and uploaded object.
3. Read the uploaded object from the public bucket.
4. Copy the object into the private bucket.
5. Insert an execution record into DynamoDB.
6. Record the operation status as `Success` when the copy completed successfully.

After updating the script, I deployed the Lambda function.

---

## Step 9: Configure the S3 Event Notification

Next, I opened the public S3 bucket:

```text
nautilus-public-221033535
```

I navigated to:

```text
S3 Bucket
    |
    v
Properties
    |
    v
Event Notifications
```

I created an event notification with the following configuration:

```text
Event Name: Lambda-S3-Trigger
Event Type: s3:ObjectCreated:*
Destination: Lambda Function
Lambda Function: nautilus-copyfunction
```

This event notification allowed S3 to invoke the Lambda function whenever a new object was created in the public bucket.

The required Lambda invocation permission was also configured through the S3 trigger integration.

---

## Step 10: Upload the Test File

To test the complete workflow, I uploaded a test file named:

```text
sample.zip
```

I uploaded this file into the public S3 bucket:

```text
nautilus-public-221033535
```

The upload generated an S3 object-created event.

That event automatically triggered the Lambda function.

---

## Step 11: Verify the Lambda Execution

I opened the Lambda function:

```text
nautilus-copyfunction
```

I checked the monitoring and execution logs to verify that the function was invoked successfully.

The Lambda function processed the uploaded file and performed the following operations:

- Read `sample.zip` from the public bucket.
- Copied the file into the private bucket.
- Recorded the operation details in DynamoDB.

---

## Step 12: Verify the Private S3 Bucket

I opened the destination bucket:

```text
nautilus-private-221033535
```

I checked the Objects section.

The uploaded file was successfully copied:

```text
sample.zip
```

This confirmed that the Lambda function had successfully transferred the file from the public bucket to the private bucket.

---

## Step 13: Verify DynamoDB Execution Logs

Finally, I opened the DynamoDB table:

```text
nautilus-S3CopyLogs
```

I selected **Explore table items** to inspect the recorded execution details.

The table contained an entry with the following information:

| Attribute | Recorded Value |
| ------------------ | -------------------------------- |
| LogID | Generated identifier |
| Source Bucket | `nautilus-public-221033535` |
| Destination Bucket | `nautilus-private-221033535` |
| Object Key | `sample.zip` |
| Status | `Success` |

This confirmed that the Lambda function successfully recorded the file-copy operation in DynamoDB.

---

# Event-Driven Architecture Workflow

The overall architecture of this challenge was:

```text
       User Uploads File
              |
              v
     Public S3 Bucket
  nautilus-public-221033535
              |
              v
   S3 ObjectCreated Event
              |
              v
      AWS Lambda Function
      nautilus-copyfunction
              |
       +------+------+
       |             |
       v             v
  Private S3      DynamoDB
    Bucket        Table
       |             |
       v             v
  sample.zip     Copy Logs
       |             |
       +------+------+
              |
              v
       Successful Copy
```

---

# What I Learned

From this challenge, I learned and practiced:

- How to create public and private Amazon S3 buckets.
- How S3 bucket public access settings work.
- How to configure bucket policies for public read access.
- How to create a DynamoDB table.
- How DynamoDB partition keys work.
- How to create IAM roles for AWS Lambda.
- How to configure permissions for S3, DynamoDB, and CloudWatch Logs.
- How to create and configure an AWS Lambda function.
- How to configure Lambda memory and timeout settings.
- How S3 event notifications work.
- How to integrate Amazon S3 with AWS Lambda.
- How to build an event-driven file-processing workflow.
- How to copy files between S3 buckets automatically.
- How to record application execution details in DynamoDB.
- How to verify Lambda executions and troubleshoot event-driven workflows.

# Challenge Status

Day 46 — Completed Successfully ✅

**Created public and private S3 buckets, configured a DynamoDB logging table and IAM execution role, deployed the `nautilus-copyfunction` Lambda function, and configured an S3 event trigger. Successfully tested the workflow using `sample.zip` and verified that the file was copied to the private bucket and the execution details were recorded in DynamoDB with a `Success` status.**

100 Days. 100 Challenges. One Cloud Journey. ☁️🚀

I will continue documenting each challenge and improving my practical AWS cloud skills step by step.
