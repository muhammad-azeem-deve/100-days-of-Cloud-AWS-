# Day 34 - AWS Lambda Verification Commands

This file contains **only AWS CLI verification commands** used for Day 34 of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The Lambda function was created using the AWS Management Console. The following commands are used to verify the deployed resources.

---

# 1. Verify AWS CLI Configuration

Check the AWS CLI version:

```bash
aws --version
```

---

# 2. Verify AWS Account Identity

Check the AWS account currently connected to the CLI:

```bash
aws sts get-caller-identity
```

Display only the AWS account ID:

```bash
aws sts get-caller-identity \
    --query 'Account' \
    --output text
```

---

# 3. Verify Lambda Function Exists

List Lambda functions in the required region:

```bash
aws lambda list-functions \
    --region us-east-1
```

Check the specific Lambda function:

```bash
aws lambda get-function \
    --function-name xfusion-lambda-cli \
    --region us-east-1
```

---

# 4. Verify Lambda Function Configuration

Display the function name, runtime, handler, and execution role:

```bash
aws lambda get-function-configuration \
    --function-name xfusion-lambda-cli \
    --region us-east-1 \
    --query '{FunctionName:FunctionName,Runtime:Runtime,Handler:Handler,Role:Role,State:State,LastUpdateStatus:LastUpdateStatus}' \
    --output table
```

Expected configuration:

```text
FunctionName       : xfusion-lambda-cli
Runtime            : Python
Handler            : lambda_function.lambda_handler
Role               : lambda_execution_role ARN
State              : Active
LastUpdateStatus   : Successful
```

---

# 5. Verify IAM Execution Role

Check whether the required IAM role exists:

```bash
aws iam get-role \
    --role-name lambda_execution_role
```

Display the role name and ARN:

```bash
aws iam get-role \
    --role-name lambda_execution_role \
    --query 'Role.[RoleName,Arn]' \
    --output table
```

---

# 6. Verify Attached IAM Policies

Check the policies attached to the execution role:

```bash
aws iam list-attached-role-policies \
    --role-name lambda_execution_role
```

Verify that the following policy is attached:

```text
AWSLambdaBasicExecutionRole
```

---

# 7. Verify Lambda Function Response

Invoke the Lambda function to verify its execution:

```bash
aws lambda invoke \
    --function-name xfusion-lambda-cli \
    --region us-east-1 \
    --cli-binary-format raw-in-base64-out \
    --payload '{}' \
    response.json
```

The command saves the function response in:

```text
response.json
```

Display the response:

```bash
cat response.json
```

Expected response:

```json
{
    "statusCode": 200,
    "body": "Welcome to KKE AWS Labs!"
}
```

---

# 8. Verify Lambda Execution Status

The invocation command returns execution metadata, including:

```text
StatusCode: 200
```

A status code of `200` indicates that the invocation request was successfully processed.

The function's returned `statusCode` is also expected to be `200`.

---

# Final Verification

The final resource configuration:

```text
Lambda Function : xfusion-lambda-cli
Runtime         : Python
Handler         : lambda_function.lambda_handler
IAM Role        : lambda_execution_role
Region          : us-east-1
Response Code   : 200
Response Body   : Welcome to KKE AWS Labs!
Status          : Successfully Verified
```

---

# Day 34 Result

**Day 34 — Completed Successfully ✅**

AWS Lambda function created through the AWS Management Console and verified using AWS CLI.

100 Days. 100 Challenges. One Cloud Journey. ☁️🚀
