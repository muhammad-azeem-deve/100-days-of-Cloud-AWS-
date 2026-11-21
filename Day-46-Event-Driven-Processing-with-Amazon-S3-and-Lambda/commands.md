# Day 46 - AWS S3, Lambda and DynamoDB Commands

This file contains the AWS CLI commands related to **Day 46** of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The task was to automate file transfers between public and private S3 buckets using AWS Lambda, with DynamoDB used to maintain execution logs.

---

# 1. Verify AWS CLI

Check whether AWS CLI is installed:

```bash
aws --version
```

---

# 2. Verify AWS Account

Check the currently configured AWS identity:

```bash
aws sts get-caller-identity
```

---

# 3. Configure AWS Region

The required AWS region was:

```text
us-east-1
```

To configure the default AWS region:

```bash
aws configure set region us-east-1
```

---

# 4. Create the Public S3 Bucket

Create the public S3 bucket:

```bash
aws s3api create-bucket \
    --bucket nautilus-public-221033535 \
    --region us-east-1
```

Disable the public access block for the public bucket:

```bash
aws s3api put-public-access-block \
    --bucket nautilus-public-221033535 \
    --region us-east-1 \
    --public-access-block-configuration \
    'BlockPublicAcls=false,IgnorePublicAcls=false,BlockPublicPolicy=false,RestrictPublicBuckets=false'
```

Example bucket policy to allow public read access to objects:

```bash
aws s3api put-bucket-policy \
    --bucket nautilus-public-221033535 \
    --region us-east-1 \
    --policy file://public-bucket-policy.json
```

Example `public-bucket-policy.json`:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadAccess",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::nautilus-public-221033535/*"
        }
    ]
}
```

**Note:** Public read access is not the same as public upload permission. Public uploads require a separate `s3:PutObject` permission and should only be enabled if explicitly required.

---

# 5. Create the Private S3 Bucket

Create the private destination bucket:

```bash
aws s3api create-bucket \
    --bucket nautilus-private-221033535 \
    --region us-east-1
```

Enable all public access blocks:

```bash
aws s3api put-public-access-block \
    --bucket nautilus-private-221033535 \
    --region us-east-1 \
    --public-access-block-configuration \
    'BlockPublicAcls=true,IgnorePublicAcls=true,BlockPublicPolicy=true,RestrictPublicBuckets=true'
```

This ensures that public access is blocked for the private bucket.

---

# 6. Verify S3 Buckets

List all S3 buckets:

```bash
aws s3 ls
```

Check the public bucket:

```bash
aws s3api get-bucket-location \
    --bucket nautilus-public-221033535
```

Check the private bucket:

```bash
aws s3api get-bucket-location \
    --bucket nautilus-private-221033535
```

Check public access settings:

```bash
aws s3api get-public-access-block \
    --bucket nautilus-private-221033535
```

---

# 7. Create the DynamoDB Table

Create the DynamoDB table:

```bash
aws dynamodb create-table \
    --table-name nautilus-S3CopyLogs \
    --attribute-definitions \
        AttributeName=LogID,AttributeType=S \
    --key-schema \
        AttributeName=LogID,KeyType=HASH \
    --billing-mode PAY_PER_REQUEST \
    --region us-east-1
```

The table uses:

```text
Table Name: nautilus-S3CopyLogs
Partition Key: LogID
Data Type: String
```

---

# 8. Verify DynamoDB Table

Describe the DynamoDB table:

```bash
aws dynamodb describe-table \
    --table-name nautilus-S3CopyLogs \
    --region us-east-1
```

List DynamoDB tables:

```bash
aws dynamodb list-tables \
    --region us-east-1
```

---

# 9. Create the IAM Role

Create a trust policy file named `trust-policy.json`.

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "Service": "lambda.amazonaws.com"
            },
            "Action": "sts:AssumeRole"
        }
    ]
}
```

Create the IAM role:

```bash
aws iam create-role \
    --role-name lambda_execution_role \
    --assume-role-policy-document file://trust-policy.json
```

---

# 10. Configure IAM Permissions

Create an IAM policy file named `lambda-permissions.json`.

The policy must allow:

- Reading objects from the public S3 bucket.
- Writing objects to the private S3 bucket.
- Inserting records into DynamoDB.
- Creating and writing CloudWatch Logs.

Example policy:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetObject"
            ],
            "Resource": "arn:aws:s3:::nautilus-public-221033535/*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:PutObject"
            ],
            "Resource": "arn:aws:s3:::nautilus-private-221033535/*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "dynamodb:PutItem"
            ],
            "Resource": "arn:aws:dynamodb:us-east-1:ACCOUNT_ID:table/nautilus-S3CopyLogs"
        },
        {
            "Effect": "Allow",
            "Action": [
                "logs:CreateLogGroup",
                "logs:CreateLogStream",
                "logs:PutLogEvents"
            ],
            "Resource": "arn:aws:logs:us-east-1:ACCOUNT_ID:*"
        }
    ]
}
```

Replace `ACCOUNT_ID` with the actual AWS account ID.

Create the IAM policy:

```bash
aws iam create-policy \
    --policy-name lambda-s3-dynamodb-policy \
    --policy-document file://lambda-permissions.json
```

Attach the policy to the role:

```bash
aws iam attach-role-policy \
    --role-name lambda_execution_role \
    --policy-arn arn:aws:iam::ACCOUNT_ID:policy/lambda-s3-dynamodb-policy
```

**Note:** IAM roles are global resources. Replace the account ID with the actual lab account ID. The policy above is an example of the required permissions; use the exact permissions specified by the supplied Lambda script and lab requirements.

---

# 11. Create the Lambda Function

Package the Lambda code:

```bash
zip function.zip lambda_function.py
```

Retrieve the IAM role ARN:

```bash
aws iam get-role \
    --role-name lambda_execution_role \
    --query 'Role.Arn' \
    --output text
```

Create the Lambda function:

```bash
aws lambda create-function \
    --function-name nautilus-copyfunction \
    --runtime python3.12 \
    --role arn:aws:iam::ACCOUNT_ID:role/lambda_execution_role \
    --handler lambda_function.lambda_handler \
    --zip-file fileb://function.zip \
    --timeout 10 \
    --memory-size 128 \
    --region us-east-1
```

**Note:** The runtime and handler must match the supplied Lambda script. The command above uses Python 3.12 as an example.

---

# 12. Verify Lambda Configuration

Check the Lambda function:

```bash
aws lambda get-function \
    --function-name nautilus-copyfunction \
    --region us-east-1
```

Check memory and timeout:

```bash
aws lambda get-function-configuration \
    --function-name nautilus-copyfunction \
    --region us-east-1 \
    --query '[FunctionName,MemorySize,Timeout,Role]'
```

Expected configuration:

```text
Function Name: nautilus-copyfunction
Memory: 128 MB
Timeout: 10 seconds
```

---

# 13. Configure S3 Event Notification

Create an event notification configuration file named `notification.json`.

```json
{
    "LambdaFunctionConfigurations": [
        {
            "Id": "S3LambdaTrigger",
            "LambdaFunctionArn": "arn:aws:lambda:us-east-1:ACCOUNT_ID:function:nautilus-copyfunction",
            "Events": [
                "s3:ObjectCreated:*"
            ]
        }
    ]
}
```

Allow the public S3 bucket to invoke the Lambda function:

```bash
aws lambda add-permission \
    --function-name nautilus-copyfunction \
    --statement-id AllowS3Invoke \
    --action lambda:InvokeFunction \
    --principal s3.amazonaws.com \
    --source-arn arn:aws:s3:::nautilus-public-221033535 \
    --source-account ACCOUNT_ID \
    --region us-east-1
```

Configure the S3 event notification:

```bash
aws s3api put-bucket-notification-configuration \
    --bucket nautilus-public-221033535 \
    --notification-configuration file://notification.json \
    --region us-east-1
```

This configuration invokes the Lambda function whenever a new object is created in the public bucket.

---

# 14. Upload the Test File

Upload `sample.zip` into the public bucket:

```bash
aws s3 cp sample.zip \
    s3://nautilus-public-221033535/ \
    --region us-east-1
```

The upload generates an S3 object-created event.

The event automatically invokes the Lambda function.

---

# 15. Verify the Private Bucket

List the objects in the private bucket:

```bash
aws s3 ls s3://nautilus-private-221033535/ \
    --region us-east-1
```

Expected file:

```text
sample.zip
```

This confirms that the Lambda function copied the uploaded object successfully.

---

# 16. Verify DynamoDB Execution Logs

Scan the DynamoDB table:

```bash
aws dynamodb scan \
    --table-name nautilus-S3CopyLogs \
    --region us-east-1
```

The recorded item should contain information about:

```text
LogID
SourceBucket
DestinationBucket
ObjectKey
Status
```

The exact attribute names depend on the provided Lambda script.

Expected values:

```text
Source Bucket      : nautilus-public-221033535
Destination Bucket : nautilus-private-221033535
Object Key         : sample.zip
Status             : Success
```

---

# 17. Check Lambda CloudWatch Logs

Retrieve the Lambda log group:

```bash
aws logs describe-log-groups \
    --log-group-name-prefix /aws/lambda/nautilus-copyfunction \
    --region us-east-1
```

View recent log events:

```bash
aws logs tail /aws/lambda/nautilus-copyfunction \
    --since 1h \
    --region us-east-1
```

These commands help verify Lambda execution and troubleshoot errors.

---

# 18. Verify S3 Event Notification

Check the configured event notification:

```bash
aws s3api get-bucket-notification-configuration \
    --bucket nautilus-public-221033535 \
    --region us-east-1
```

The configuration should contain:

```text
s3:ObjectCreated:*
```

and the Lambda function destination.

---

# Final Verification

The final infrastructure configuration was:

```text
AWS Region: us-east-1

Public S3 Bucket:
nautilus-public-221033535

Private S3 Bucket:
nautilus-private-221033535

DynamoDB Table:
nautilus-S3CopyLogs

Partition Key:
LogID

IAM Role:
lambda_execution_role

Lambda Function:
nautilus-copyfunction

Lambda Memory:
128 MB

Lambda Timeout:
10 Seconds

S3 Event:
s3:ObjectCreated:*

Test File:
sample.zip

Copy Status:
Success
```

---

# Day 46 Result

```text
Public S3 Bucket
       |
       v
S3 ObjectCreated Event
       |
       v
AWS Lambda Function
       |
       +------------------+
       |                  |
       v                  v
Private S3 Bucket      DynamoDB
       |                  |
       v                  v
  sample.zip          Success Log
       |                  |
       +--------+---------+
                |
                v
       Task Completed
```

**Day 46 — Completed Successfully ✅**

100 Days. 100 Challenges. One Cloud Journey. ☁️🚀
