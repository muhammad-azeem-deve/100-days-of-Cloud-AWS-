# Day 33 - AWS Lambda Verification Commands

This file contains the AWS CLI commands used to verify the resources created during **Day 33** of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The IAM execution role and Lambda function were created and deployed using the AWS Management Console.

The following commands are only for verification.

---

## 1. Verify AWS Identity

To verify the AWS account and currently authenticated IAM identity:

```bash
aws sts get-caller-identity
```

---

## 2. Verify IAM Execution Role

To check whether the required IAM role exists:

```bash
aws iam get-role \
    --role-name lambda_execution_role
```

---

## 3. Verify IAM Role Trust Relationship

To verify that AWS Lambda is allowed to assume the execution role:

```bash
aws iam get-role \
    --role-name lambda_execution_role \
    --query 'Role.AssumeRolePolicyDocument' \
    --output json
```

The trust policy should include:

```json
{
    "Service": "lambda.amazonaws.com",
    "Action": "sts:AssumeRole"
}
```

---

## 4. Verify Attached IAM Policies

To check the policies attached to the execution role:

```bash
aws iam list-attached-role-policies \
    --role-name lambda_execution_role
```

The output should include:

```text
AWSLambdaBasicExecutionRole
```

---

## 5. Verify Lambda Function Exists

To verify that the Lambda function was created successfully:

```bash
aws lambda get-function \
    --function-name nautilus-lambda \
    --region us-east-1
```

---

## 6. Verify Lambda Function Configuration

To display the Lambda function configuration:

```bash
aws lambda get-function-configuration \
    --function-name nautilus-lambda \
    --region us-east-1
```

---

## 7. Verify Function Name, Runtime, Handler and Execution Role

To display the important configuration details in table format:

```bash
aws lambda get-function-configuration \
    --function-name nautilus-lambda \
    --region us-east-1 \
    --query '{FunctionName:FunctionName,Runtime:Runtime,Handler:Handler,Role:Role,State:State,LastUpdateStatus:LastUpdateStatus}' \
    --output table
```

Expected configuration:

```text
FunctionName: nautilus-lambda
Runtime:      python3.x
Handler:      lambda_function.lambda_handler
Role:         lambda_execution_role ARN
State:        Active
```

---

## 8. Verify Lambda Function Execution

To invoke the deployed Lambda function and save its response:

```bash
aws lambda invoke \
    --function-name nautilus-lambda \
    --region us-east-1 \
    response.json
```

---

## 9. Display Lambda Function Response

To check the returned response:

```bash
cat response.json
```

Expected output:

```json
{
    "statusCode": 200,
    "body": "Welcome to KKE AWS Labs!"
}
```

---

## 10. Verify Lambda Execution Status

To check the invocation status and HTTP status code:

```bash
aws lambda invoke \
    --function-name nautilus-lambda \
    --region us-east-1 \
    --query '{StatusCode:StatusCode,FunctionError:FunctionError}' \
    --output table response.json
```

A successful synchronous invocation should return:

```text
StatusCode: 200
FunctionError: None
```

---

## 11. Verify Lambda Function Logs

To list the CloudWatch log streams created for the Lambda function:

```bash
aws logs describe-log-streams \
    --log-group-name /aws/lambda/nautilus-lambda \
    --order-by LastEventTime \
    --descending \
    --region us-east-1
```

To retrieve the latest execution logs:

```bash
aws logs tail /aws/lambda/nautilus-lambda \
    --since 10m \
    --region us-east-1
```

---

## Final Verification

The following resources and configurations were verified:

```text
IAM Role:
lambda_execution_role

Attached Policy:
AWSLambdaBasicExecutionRole

Lambda Function:
nautilus-lambda

Runtime:
Python 3.x

Handler:
lambda_function.lambda_handler

AWS Region:
us-east-1

Status Code:
200

Response:
Welcome to KKE AWS Labs!

Status:
Successfully Created, Deployed and Verified
```

**Day 33 — Completed Successfully ✅**
