# Day 48 - Automating Infrastructure Deployment with AWS CloudFormation

## Challenge Overview

This is Day 48 of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The challenge was related to **AWS CloudFormation, Infrastructure as Code (IaC), IAM Roles, and AWS Lambda**.

The Nautilus DevOps team wanted to deploy a serverless function using Infrastructure as Code instead of manually creating AWS resources through the Management Console.

For this task, I was required to create a CloudFormation template on the AWS client machine and use it to deploy an IAM execution role and a Python-based AWS Lambda function.

The CloudFormation stack had to be deployed with a specific name in the `us-east-1` region.

### Challenge Requirement

The requirements were:

1. Access the AWS client machine.
2. Create a CloudFormation template at `/root/datacenter-lambda.yml`.
3. Create an IAM role named `lambda_execution_role`.
4. Configure the IAM role with the required Lambda execution permissions.
5. Create a Python-based Lambda function named `datacenter-lambda`.
6. Configure the Lambda function with 128 MB of memory.
7. Set the Lambda timeout to 10 seconds.
8. Configure the Lambda function to return:
   - Status Code: `200`
   - Response Body: `Welcome to KKE AWS Labs!`
9. Deploy the CloudFormation template as a stack named `datacenter-lambda-app`.
10. Use the `us-east-1` AWS region.
11. Verify that the stack reaches `CREATE_COMPLETE`.
12. Invoke the Lambda function and verify its response.

---

## Objectives

The objectives of this challenge are:

- Understand Infrastructure as Code (IaC).
- Learn how AWS CloudFormation works.
- Create AWS resources using YAML templates.
- Understand CloudFormation stacks.
- Create IAM roles and configure permissions.
- Deploy serverless applications using AWS Lambda.
- Configure Lambda memory and timeout settings.
- Deploy infrastructure using AWS CLI.
- Verify CloudFormation stack status.
- Invoke a Lambda function and validate its response.

---

## Environment

| Item | Details |
|---|---|
| Challenge | 100 Days of Cloud (AWS) |
| Platform | KodeKloud |
| Day | 48 |
| Cloud Provider | Amazon Web Services (AWS) |
| Services | AWS CloudFormation, AWS Lambda, IAM |
| AWS Client | AWS Client Machine |
| Template File | `/root/datacenter-lambda.yml` |
| Stack Name | `datacenter-lambda-app` |
| IAM Role Name | `lambda_execution_role` |
| Lambda Function | `datacenter-lambda` |
| Runtime | Python |
| Memory | 128 MB |
| Timeout | 10 seconds |
| Region | `us-east-1` |
| Status | Completed |

---

# Solution

## Step 1: Access the AWS Client Machine

First, I accessed the AWS client machine provided by the KodeKloud lab.

I used the terminal to execute AWS CLI commands and create the CloudFormation template.

I verified the current user:

```bash
whoami
```

Since the required template location was `/root`, I made sure I had the necessary permissions to create the file.

## Step 2: Verify AWS CLI Configuration

Before deploying the infrastructure, I checked the AWS CLI version:

```bash
aws --version
```

I also verified the active AWS identity:

```bash
aws sts get-caller-identity
```

The challenge required all resources to be created in:

```text
us-east-1
```

## Step 3: Create the CloudFormation Template

The required CloudFormation template location was:

```text
/root/datacenter-lambda.yml
```

I created the file using:

```bash
vi /root/datacenter-lambda.yml
```

The template defined two main AWS resources:

1. IAM Execution Role
2. AWS Lambda Function

## Step 4: Define the CloudFormation Template

I added the following YAML configuration to the template:

```yaml
AWSTemplateFormatVersion: '2010-09-09'

Description: CloudFormation template to deploy a Lambda function

Resources:

  LambdaExecutionRole:
    Type: AWS::IAM::Role
    Properties:
      RoleName: lambda_execution_role
      AssumeRolePolicyDocument:
        Version: '2012-10-17'
        Statement:
          - Effect: Allow
            Principal:
              Service:
                - lambda.amazonaws.com
            Action:
              - sts:AssumeRole
      ManagedPolicyArns:
        - arn:aws:iam::aws:policy/service-role/AWSLambdaBasicExecutionRole

  DatacenterLambda:
    Type: AWS::Lambda::Function
    Properties:
      FunctionName: datacenter-lambda
      Runtime: python3.12
      Handler: index.lambda_handler
      Role: !GetAtt LambdaExecutionRole.Arn
      MemorySize: 128
      Timeout: 10
      Code:
        ZipFile: |
          import json

          def lambda_handler(event, context):
              return {
                  'statusCode': 200,
                  'body': 'Welcome to KKE AWS Labs!'
              }
```

### Explanation of the Template

**1. AWS::IAM::Role**

This resource creates the IAM execution role required by the Lambda function.

- Role Name: `lambda_execution_role`
- Trusted Service: `lambda.amazonaws.com`
- Managed Policy: `AWSLambdaBasicExecutionRole`

The managed policy allows Lambda to write execution logs to Amazon CloudWatch Logs.

**2. AWS::Lambda::Function**

This resource creates the serverless Lambda function.

- Function Name: `datacenter-lambda`
- Runtime: Python
- Handler: `index.lambda_handler`
- Memory: 128 MB
- Timeout: 10 seconds
- Execution Role: `lambda_execution_role`

**3. Lambda Function Response**

The Python function returns the following response:

```python
{
    'statusCode': 200,
    'body': 'Welcome to KKE AWS Labs!'
}
```

## Step 5: Save the CloudFormation Template

After adding the YAML configuration, I saved the file.

The final file location was:

```text
/root/datacenter-lambda.yml
```

I verified the file:

```bash
ls -l /root/datacenter-lambda.yml
```

I also checked its contents:

```bash
cat /root/datacenter-lambda.yml
```

## Step 6: Validate the CloudFormation Template

Before deploying the stack, I validated the template using AWS CLI:

```bash
aws cloudformation validate-template \
    --template-body file:///root/datacenter-lambda.yml \
    --region us-east-1
```

This command checks whether the CloudFormation template is valid.

## Step 7: Deploy the CloudFormation Stack

After validating the template, I deployed it using the AWS CLI.

The required stack name was:

```text
datacenter-lambda-app
```

I executed:

```bash
aws cloudformation create-stack \
    --stack-name datacenter-lambda-app \
    --template-body file:///root/datacenter-lambda.yml \
    --capabilities CAPABILITY_NAMED_IAM \
    --region us-east-1
```

### Explanation

- `--stack-name`: Specifies the CloudFormation stack name.
- `--template-body`: Specifies the location of the YAML template.
- `--capabilities CAPABILITY_NAMED_IAM`: Allows CloudFormation to create an IAM resource with a custom name.
- `--region`: Specifies the AWS region.

## Step 8: Verify CloudFormation Stack Status

After submitting the deployment request, I checked the stack status:

```bash
aws cloudformation describe-stacks \
    --stack-name datacenter-lambda-app \
    --region us-east-1
```

I waited for the stack creation process to complete.

The expected final status was:

```text
CREATE_COMPLETE
```

I also verified the stack using:

```bash
aws cloudformation wait stack-create-complete \
    --stack-name datacenter-lambda-app \
    --region us-east-1
```

## Step 9: Verify the Created Lambda Function

After successful stack deployment, I checked the Lambda function:

```bash
aws lambda get-function \
    --function-name datacenter-lambda \
    --region us-east-1
```

I verified the function configuration:

```bash
aws lambda get-function-configuration \
    --function-name datacenter-lambda \
    --region us-east-1
```

The expected configuration was:

```text
Function Name : datacenter-lambda
Runtime       : Python
Memory        : 128 MB
Timeout       : 10 seconds
```

## Step 10: Verify the IAM Execution Role

I verified that the IAM role was created:

```bash
aws iam get-role \
    --role-name lambda_execution_role
```

The role was configured to allow the AWS Lambda service to assume it.

## Step 11: Invoke the Lambda Function

Finally, I invoked the Lambda function using AWS CLI:

```bash
aws lambda invoke \
    --function-name datacenter-lambda \
    --region us-east-1 \
    response.json
```

This command executes the Lambda function and saves its response to `response.json`.

## Step 12: Verify the Lambda Response

I checked the function output:

```bash
cat response.json
```

The expected response was:

```json
{
    "statusCode": 200,
    "body": "Welcome to KKE AWS Labs!"
}
```

This confirmed that the Lambda function executed successfully and returned the required response.

---

# Infrastructure Architecture

The following diagram represents the resources deployed through CloudFormation:

```text
             AWS Cloud
                 |
                 v
        AWS CloudFormation
                 |
                 v
       datacenter-lambda-app
                 |
          +------+------+
          |             |
          v             v
      AWS IAM        AWS Lambda
        Role           Function
          |             |
          v             v
 lambda_execution   datacenter-lambda
      _role              |
                         |
                    Python Runtime
                         |
                         v
                   lambda_handler
                         |
                         v
                   HTTP Status: 200
                         |
                         v
             Welcome to KKE AWS Labs!
```

---

# Final Verification

The final infrastructure configuration was:

| Resource | Configuration | Status |
|---|---|---|
| CloudFormation Stack | `datacenter-lambda-app` | CREATE_COMPLETE |
| IAM Role | `lambda_execution_role` | Created |
| Lambda Function | `datacenter-lambda` | Created |
| Runtime | Python | Configured |
| Memory | 128 MB | Configured |
| Timeout | 10 seconds | Configured |
| Response Status | 200 | Verified |
| Response Body | Welcome to KKE AWS Labs! | Verified |
| AWS Region | `us-east-1` | Confirmed |

---

# What I Learned

From this challenge, I learned and practiced:

- How Infrastructure as Code works.
- How to create AWS infrastructure using CloudFormation.
- How to write CloudFormation templates in YAML.
- How to create IAM roles using CloudFormation.
- How IAM trust policies allow AWS services to assume roles.
- How to attach AWS managed policies to IAM roles.
- How to deploy AWS Lambda functions using CloudFormation.
- How to configure Python Lambda functions.
- How to configure Lambda memory and timeout settings.
- How to deploy CloudFormation stacks using AWS CLI.
- How to validate CloudFormation templates.
- How to monitor CloudFormation stack deployment.
- How to invoke Lambda functions using AWS CLI.
- How to verify Lambda function responses.

# Challenge Status

Day 48 — Completed Successfully ✅

**Created the CloudFormation template `datacenter-lambda.yml`, deployed the `datacenter-lambda-app` stack, created the `lambda_execution_role` IAM role and `datacenter-lambda` Python function, and successfully verified the expected Lambda response.**

100 Days. 100 Challenges. One Cloud Journey. ☁️🚀

I will continue documenting each challenge and building my practical AWS cloud engineering skills step by step.
