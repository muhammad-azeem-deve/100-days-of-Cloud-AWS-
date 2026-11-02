# Day 33 - Create an AWS Lambda Function

## Challenge Overview

This is Day 33 of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The challenge was related to **AWS Lambda, IAM Roles, Serverless Computing, and Function Deployment**.

The Nautilus DevOps team decided to introduce serverless computing into their infrastructure by using AWS Lambda. The objective was to create a simple Lambda function that returns a custom greeting message when executed.

Before creating the Lambda function, I first created a dedicated IAM execution role with the required permissions. After creating the role, I used it while configuring and deploying the Lambda function through the AWS Management Console.

### Challenge Requirement

The requirements were:

1. Access the AWS Management Console.
2. Use the `us-east-1` AWS region.
3. Create an IAM role named `lambda_execution_role`.
4. Configure the role to allow AWS Lambda to assume it.
5. Attach the `AWSLambdaBasicExecutionRole` permission policy.
6. Create an AWS Lambda function named `nautilus-lambda`.
7. Select Python as the runtime.
8. Use the previously created `lambda_execution_role`.
9. Configure the function to return the following response:
   - Status Code: `200`
   - Body: `Welcome to KKE AWS Labs!`
10. Deploy and test the Lambda function.

---

## Objectives

The objectives of this challenge are:

- Understand the basics of AWS Lambda.
- Understand serverless computing.
- Create an IAM execution role for Lambda.
- Configure IAM trust relationships.
- Attach permissions to an IAM role.
- Create a Lambda function using the AWS Console.
- Configure a Python runtime.
- Associate an existing IAM role with a Lambda function.
- Write and deploy Lambda function code.
- Test the Lambda function.
- Verify the function execution and response.
- Understand the relationship between IAM and AWS Lambda.

---

## Environment

| Item | Details |
| ----------------- | ------------------------------ |
| Challenge | 100 Days of Cloud (AWS) |
| Platform | KodeKloud |
| Day | 33 |
| Cloud Provider | Amazon Web Services (AWS) |
| Services | AWS IAM and AWS Lambda |
| Region | `us-east-1` |
| IAM Role Name | `lambda_execution_role` |
| IAM Policy | `AWSLambdaBasicExecutionRole` |
| Lambda Function | `nautilus-lambda` |
| Runtime | Python 3.x |
| Handler | `lambda_function.lambda_handler` |
| Status Code | `200` |
| Response Body | `Welcome to KKE AWS Labs!` |
| Deployment Method | AWS Management Console |
| Status | Completed |

---

# Solution

## Step 1: Access the AWS Management Console

First, I accessed the AWS Management Console using the temporary credentials provided by the KodeKloud lab environment.

I made sure to select the required AWS region:

```text
us-east-1
```

All resources for this challenge were created in the specified region.

---

## Step 2: Open IAM Service

Before creating the Lambda function, I needed to create an IAM execution role.

I opened the **IAM (Identity and Access Management)** service from the AWS Management Console.

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
    Create Role
```

---

## Step 3: Create the IAM Execution Role

I selected **Create role** to create a new IAM role for the Lambda function.

Under Trusted entity type, I selected:

```text
AWS Service
```

For the use case, I selected:

```text
Lambda
```

This configuration allows the AWS Lambda service to assume the IAM role when executing the function.

---

## Step 4: Attach the Required Permission Policy

In the permissions section, I searched for the following AWS managed policy:

```text
AWSLambdaBasicExecutionRole
```

I selected and attached this policy to the IAM role.

This policy provides the basic permissions required by Lambda to send execution logs to Amazon CloudWatch Logs.

The permission policy was:

```text
AWSLambdaBasicExecutionRole
```

After selecting the policy, I proceeded to the next step.

---

## Step 5: Configure the IAM Role Name

I configured the IAM role using the required name:

```text
Role Name: lambda_execution_role
```

I reviewed the role configuration and selected **Create role**.

The IAM execution role was successfully created.

The final role configuration was:

```text
IAM Role Name: lambda_execution_role

Trusted Entity: AWS Lambda

Permission Policy:
AWSLambdaBasicExecutionRole

Region: Global IAM Resource
```

---

## Step 6: Open AWS Lambda Service

After creating the IAM role, I opened the **AWS Lambda** service from the AWS Management Console.

The navigation path was:

```text
AWS Management Console
        |
        v
      Lambda
        |
        v
   Functions
        |
        v
  Create Function
```

I selected **Create function** to start configuring the Lambda function.

---

## Step 7: Configure the Lambda Function

I selected:

```text
Author from scratch
```

Then I configured the basic function information.

The configuration was:

```text
Function Name: nautilus-lambda

Runtime: Python 3.x

Architecture: x86_64
```

I used an available Python 3.x runtime.

---

## Step 8: Configure Execution Permissions

Under the permissions section, I expanded:

```text
Change default execution role
```

I selected:

```text
Use an existing role
```

From the existing IAM roles, I selected:

```text
lambda_execution_role
```

This connected the previously created IAM execution role with the Lambda function.

The permission configuration was:

```text
Execution Role: lambda_execution_role
```

After reviewing the configuration, I selected **Create function**.

The Lambda function was successfully created.

---

## Step 9: Write the Lambda Function Code

After creating the function, I opened the **Code** tab.

Inside the code editor, I opened the default Python file:

```text
lambda_function.py
```

I replaced the default code with the following Python code:

```python
def lambda_handler(event, context):
    return {
        "statusCode": 200,
        "body": "Welcome to KKE AWS Labs!"
    }
```

### Code Explanation

- `lambda_handler` is the entry point of the Lambda function.
- `event` contains the input data passed to the function.
- `context` provides runtime information about the Lambda execution.
- `statusCode: 200` represents a successful response.
- `body` contains the custom greeting message.

---

## Step 10: Deploy the Lambda Function

After writing the Python code, I selected the **Deploy** button in the Lambda Code tab.

This saved and deployed the updated function code.

The function configuration was:

```text
Function Name: nautilus-lambda

Runtime: Python 3.x

Handler: lambda_function.lambda_handler

Execution Role: lambda_execution_role
```

The deployment completed successfully.

---

## Step 11: Configure a Test Event

After deploying the function, I opened the **Test** tab.

I created a new test event with the following configuration:

```text
Event Name: test
```

I kept the default JSON test event because the function did not require any specific input parameters.

I saved the test event.

---

## Step 12: Execute the Lambda Function

I selected the **Test** button to execute the Lambda function.

AWS Lambda executed the Python handler using the configured execution role.

The function returned the expected response.

### Expected Output

```json
{
    "statusCode": 200,
    "body": "Welcome to KKE AWS Labs!"
}
```

The execution was successful.

---

# Verification

After completing the deployment, I verified the resources through the AWS Management Console.

## 1. Verify IAM Execution Role

I navigated to:

```text
IAM
  -> Roles
  -> lambda_execution_role
```

I checked the following details:

```text
Role Name: lambda_execution_role

Trusted Entity: Lambda

Attached Policy:
AWSLambdaBasicExecutionRole
```

I also checked the Trust relationships tab to confirm that AWS Lambda was allowed to assume the role.

## 2. Verify Lambda Function

I navigated to:

```text
Lambda
  -> Functions
  -> nautilus-lambda
```

I verified the following configuration:

```text
Function Name: nautilus-lambda

Runtime: Python 3.x

Execution Role: lambda_execution_role

Handler: lambda_function.lambda_handler
```

## 3. Verify Deployed Code

Inside the Lambda Code tab, I confirmed that the deployed Python code was:

```python
def lambda_handler(event, context):
    return {
        "statusCode": 200,
        "body": "Welcome to KKE AWS Labs!"
    }
```

## 4. Verify Function Execution

I executed the function using the Test button.

The response was:

```text
Status Code: 200

Response Body:
Welcome to KKE AWS Labs!
```

This confirmed that the Lambda function was deployed and working as expected.

---

# AWS Lambda Deployment Workflow

The overall workflow for this challenge was:

```text
AWS Management Console
        |
        v
       IAM
        |
        v
    Create Role
        |
        v
lambda_execution_role
        |
        v
Attach AWSLambdaBasicExecutionRole
        |
        v
    AWS Lambda
        |
        v
  Create Function
        |
        v
  nautilus-lambda
        |
        v
 Select Python Runtime
        |
        v
 Use Existing IAM Role
        |
        v
 Write Python Function
        |
        v
       Deploy
        |
        v
     Test Function
        |
        v
  Status Code: 200
        |
        v
Welcome to KKE AWS Labs!
```

---

# What I Learned

From this challenge, I learned and practiced:

- How to create an IAM execution role for AWS Lambda.
- How to configure AWS service trust relationships.
- How to attach AWS managed policies to IAM roles.
- How to use `AWSLambdaBasicExecutionRole`.
- How AWS Lambda works as a serverless computing service.
- How to create a Lambda function using the AWS Console.
- How to configure a Python runtime.
- How to associate an existing IAM role with a Lambda function.
- How to write and deploy Python code in AWS Lambda.
- How to configure and execute Lambda test events.
- How to verify Lambda function responses.
- How IAM permissions and Lambda execution roles work together.
- How AWS Lambda integrates with CloudWatch Logs.

# Challenge Status

Day 33 — Completed Successfully ✅

**Created the** **`lambda_execution_role`** **IAM role, attached the** **`AWSLambdaBasicExecutionRole`** **policy, created the** **`nautilus-lambda`** **function using Python, deployed the function code, and successfully verified the expected response with status code** **`200`**.

100 Days. 100 Challenges. One Cloud Journey. ☁️🚀

I will continue documenting each challenge and building my practical AWS cloud skills step by step.
