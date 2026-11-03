# Day 34 - Create a Lambda Function Using CLI

## Challenge Overview

This is Day 34 of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The challenge was related to **AWS Lambda, Serverless Computing, IAM Roles, and Function Deployment**.

The Nautilus DevOps team continues to explore serverless architecture by setting up another Lambda function. The function was required to return a custom greeting message and demonstrate the capabilities of AWS Lambda.

Although the lab title mentions AWS CLI, I completed the resource creation using the **AWS Management Console** to practice the web interface. I then verified the deployed resources using both the AWS Console and AWS CLI.

### Challenge Requirement

The requirements were:

1. Access the AWS Management Console.
2. Use the `us-east-1` AWS region.
3. Create a Python script named `lambda_function.py`.
4. The function must return the body `Welcome to KKE AWS Labs!`.
5. The function must return status code `200`.
6. Package the Python script into a ZIP file named `function.zip`.
7. Create a Lambda function named `xfusion-lambda-cli`.
8. Use Python as the runtime.
9. Use the existing IAM execution role named `lambda_execution_role`.
10. Verify the Lambda function and its response.

---

## Objectives

The objectives of this challenge are:

- Understand AWS Lambda and serverless computing.
- Create and deploy a Lambda function.
- Understand Lambda execution roles.
- Configure an IAM role for Lambda.
- Write a Python Lambda handler.
- Package Python code into a ZIP deployment package.
- Configure the Lambda runtime and handler.
- Test a Lambda function using the AWS Console.
- Verify Lambda resources using AWS CLI commands.

---

## Environment

| Item | Details |
| ----------------- | ------------------------------ |
| Challenge | 100 Days of Cloud (AWS) |
| Platform | KodeKloud |
| Day | 34 |
| Cloud Provider | Amazon Web Services (AWS) |
| Service | AWS Lambda |
| Region | `us-east-1` |
| Function Name | `xfusion-lambda-cli` |
| Runtime | Python |
| Handler | `lambda_function.lambda_handler` |
| IAM Role | `lambda_execution_role` |
| Python File | `lambda_function.py` |
| Deployment Package | `function.zip` |
| Expected Status Code | `200` |
| Expected Response | `Welcome to KKE AWS Labs!` |
| Status | Completed |

---

# Solution

## Step 1: Access the AWS Management Console

First, I accessed the AWS Management Console using the temporary credentials provided by the KodeKloud lab environment.

I selected the required AWS region:

```text
us-east-1
```

All resources for this challenge were created in this region.

---

## Step 2: Check the IAM Execution Role

Before creating the Lambda function, I opened the IAM service.

The navigation path was:

```text
AWS Management Console
        |
        v
       IAM
        |
        v
      Roles
        |
        v
lambda_execution_role
```

I checked whether the required IAM role already existed.

The required role was:

```text
lambda_execution_role
```

I opened the role and verified that its trusted entity allowed the AWS Lambda service to assume the role.

The trust relationship should contain:

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

I also checked that the role had the following AWS managed policy attached:

```text
AWSLambdaBasicExecutionRole
```

This policy provides basic permissions for writing execution logs to Amazon CloudWatch Logs.

> If the role does not exist, create it through IAM, select Lambda as the trusted service, and attach the required policy.

---

## Step 3: Create the Python Lambda Function

Before deploying the Lambda function, I prepared the Python source code.

I created a Python file named:

```text
lambda_function.py
```

The file contained the following code:

```python
def lambda_handler(event, context):
    response = {
        'statusCode': 200,
        'body': 'Welcome to KKE AWS Labs!'
    }

    return response
```

### Code Explanation

- `lambda_handler` is the entry point of the Lambda function.
- `event` contains the event data passed to the function.
- `context` provides information about the Lambda execution environment.
- `statusCode` represents the HTTP-style response status.
- `body` contains the custom greeting message.

The function returns the required response whenever it is invoked.

---

## Step 4: Create the ZIP Deployment Package

After creating the Python script, I packaged it into a ZIP archive.

The deployment package name was:

```text
function.zip
```

The ZIP archive contained:

```text
function.zip
    |
    └── lambda_function.py
```

**Important:** The Python file must be located directly inside the ZIP archive, not inside another folder.

The ZIP package is uploaded to AWS Lambda during function creation.

---

## Step 5: Open AWS Lambda

I navigated to the AWS Lambda service.

The navigation path was:

```text
AWS Management Console
        |
        v
      Lambda
        |
        v
 Create function
```

I selected **Create function** to start configuring the new Lambda function.

---

## Step 6: Configure the Lambda Function

I selected the option:

```text
Author from scratch
```

Then I entered the required configuration:

```text
Function name: xfusion-lambda-cli
Runtime: Python
Architecture: x86_64
```

For the execution role, I selected:

```text
Use an existing role
```

Then I selected:

```text
lambda_execution_role
```

After checking the configuration, I selected **Create function**.

---

## Step 7: Upload the ZIP Deployment Package

After the function was created, I opened its Code section.

I selected:

```text
Upload from
     |
     v
.zip file
```

I selected the previously prepared deployment package:

```text
function.zip
```

After uploading the package, I saved the changes.

This deployed the Python code to the Lambda function.

---

## Step 8: Configure the Handler

I opened the Lambda function's Runtime settings.

I verified the handler configuration:

```text
Handler:

lambda_function.lambda_handler
```

This configuration tells AWS Lambda to:

- Open the `lambda_function.py` file.
- Execute the `lambda_handler` function.

I saved the runtime settings if any changes were required.

---

## Step 9: Test the Lambda Function

After uploading the code, I opened the **Test** tab.

I created a new test event using a simple JSON payload:

```json
{}
```

I saved the test event and selected **Test** to invoke the Lambda function.

The function executed and returned the expected response.

Expected response:

```json
{
    "statusCode": 200,
    "body": "Welcome to KKE AWS Labs!"
}
```

This confirmed that the Lambda function was working correctly.

---

## Step 10: Verify the Lambda Function in AWS Console

Finally, I returned to the Lambda function overview.

I verified the following configuration:

```text
Function Name : xfusion-lambda-cli
Runtime       : Python
Handler       : lambda_function.lambda_handler
Execution Role: lambda_execution_role
Region        : us-east-1
```

I also checked the latest test execution result.

The expected output was:

```text
Status Code : 200
Body        : Welcome to KKE AWS Labs!
```

---

# AWS CLI Verification

After completing the deployment through the AWS Management Console, I used AWS CLI commands to verify the Lambda function.

The verification commands are documented separately in:

```text
commands.md
```

These commands check:

- AWS account identity.
- Lambda function existence.
- Function configuration.
- Runtime and handler.
- IAM execution role.
- Attached IAM policies.
- Lambda function invocation.
- Expected function response.

No resource creation commands are included in `commands.md`.

---

# Lambda Function Workflow

The overall workflow for this challenge was:

```text
Python Source Code
        |
        v
lambda_function.py
        |
        v
  ZIP Packaging
        |
        v
   function.zip
        |
        v
 AWS Lambda Console
        |
        v
 Create Lambda Function
        |
        v
xfusion-lambda-cli
        |
        v
Attach IAM Execution Role
        |
        v
 Upload ZIP Package
        |
        v
 Configure Handler
        |
        v
  Test Lambda Function
        |
        v
 Verify Response
```

---

# What I Learned

From this challenge, I learned and practiced:

- How AWS Lambda works.
- How serverless computing eliminates the need to manage servers.
- How to create a Lambda execution role using IAM.
- How IAM roles provide permissions to AWS services.
- How to write a Python Lambda handler.
- How to package Python code into a ZIP deployment package.
- How to create a Lambda function using the AWS Management Console.
- How to configure the Lambda runtime and handler.
- How to upload deployment packages.
- How to test Lambda functions using the AWS Console.
- How to verify Lambda resources using AWS CLI.
- How Lambda integrates with IAM and CloudWatch.

# Challenge Status

Day 34 — Completed Successfully ✅

**Created the `xfusion-lambda-cli` AWS Lambda function using the AWS Management Console, configured the Python runtime and `lambda_execution_role`, uploaded the `function.zip` deployment package, and verified the expected response of status code `200` and body `Welcome to KKE AWS Labs!`.**

100 Days. 100 Challenges. One Cloud Journey. ☁️🚀

I will continue documenting each challenge and building my practical AWS cloud skills step by step.
