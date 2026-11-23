# Day 48 - AWS CloudFormation and Lambda Commands

This file contains the AWS CLI commands and CloudFormation configuration used for **Day 48** of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The task was to deploy an IAM execution role and a Python Lambda function using AWS CloudFormation.

---

# 1. Check AWS CLI Version

```bash
aws --version
```

---

# 2. Verify AWS Identity

```bash
aws sts get-caller-identity
```

This command verifies the active AWS credentials.

---

# 3. Set the Required AWS Region

The challenge required the `us-east-1` region.

```bash
export AWS_DEFAULT_REGION=us-east-1
```

Verify the region:

```bash
echo $AWS_DEFAULT_REGION
```

---

# 4. Create the CloudFormation Template

Create the required YAML file:

```bash
vi /root/datacenter-lambda.yml
```

Add the following configuration:

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

---

# 5. Verify the Template File

```bash
ls -l /root/datacenter-lambda.yml
```

Display the template:

```bash
cat /root/datacenter-lambda.yml
```

---

# 6. Validate the CloudFormation Template

```bash
aws cloudformation validate-template \
    --template-body file:///root/datacenter-lambda.yml \
    --region us-east-1
```

This command checks the template before deployment.

---

# 7. Deploy the CloudFormation Stack

Create the stack with the required name:

```bash
aws cloudformation create-stack \
    --stack-name datacenter-lambda-app \
    --template-body file:///root/datacenter-lambda.yml \
    --capabilities CAPABILITY_NAMED_IAM \
    --region us-east-1
```

The `CAPABILITY_NAMED_IAM` option is required because the template creates an IAM role with a custom name.

---

# 8. Check Stack Status

```bash
aws cloudformation describe-stacks \
    --stack-name datacenter-lambda-app \
    --region us-east-1
```

Display only the stack status:

```bash
aws cloudformation describe-stacks \
    --stack-name datacenter-lambda-app \
    --region us-east-1 \
    --query 'Stacks[0].StackStatus' \
    --output text
```

Expected output:

```text
CREATE_COMPLETE
```

---

# 9. Wait for Stack Creation

```bash
aws cloudformation wait stack-create-complete \
    --stack-name datacenter-lambda-app \
    --region us-east-1
```

This command waits until the stack creation operation completes successfully.

---

# 10. Verify CloudFormation Resources

```bash
aws cloudformation describe-stack-resources \
    --stack-name datacenter-lambda-app \
    --region us-east-1
```

Display resource names, types, and statuses:

```bash
aws cloudformation describe-stack-resources \
    --stack-name datacenter-lambda-app \
    --region us-east-1 \
    --query 'StackResources[*].[LogicalResourceId,ResourceType,ResourceStatus]' \
    --output table
```

---

# 11. Verify the IAM Role

```bash
aws iam get-role \
    --role-name lambda_execution_role
```

This command displays the IAM role details.

---

# 12. Verify the Lambda Function

```bash
aws lambda get-function \
    --function-name datacenter-lambda \
    --region us-east-1
```

---

# 13. Check Lambda Configuration

```bash
aws lambda get-function-configuration \
    --function-name datacenter-lambda \
    --region us-east-1
```

Display the important configuration values:

```bash
aws lambda get-function-configuration \
    --function-name datacenter-lambda \
    --region us-east-1 \
    --query '[FunctionName,Runtime,MemorySize,Timeout]' \
    --output table
```

Expected configuration:

```text
Function Name : datacenter-lambda
Memory        : 128 MB
Timeout       : 10 seconds
```

---

# 14. Invoke the Lambda Function

Execute the Lambda function:

```bash
aws lambda invoke \
    --function-name datacenter-lambda \
    --region us-east-1 \
    response.json
```

The response will be saved in:

```text
response.json
```

---

# 15. Check Lambda Execution Response

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

This confirms that the Lambda function returned the required response.

---

# 16. Verify Stack Status Again

```bash
aws cloudformation describe-stacks \
    --stack-name datacenter-lambda-app \
    --region us-east-1 \
    --query 'Stacks[0].StackStatus' \
    --output text
```

Expected output:

```text
CREATE_COMPLETE
```

---

# Important CloudFormation Commands

### Validate a Template

```bash
aws cloudformation validate-template \
    --template-body file:///root/datacenter-lambda.yml \
    --region us-east-1
```

### Create a Stack

```bash
aws cloudformation create-stack \
    --stack-name datacenter-lambda-app \
    --template-body file:///root/datacenter-lambda.yml \
    --capabilities CAPABILITY_NAMED_IAM \
    --region us-east-1
```

### Describe a Stack

```bash
aws cloudformation describe-stacks \
    --stack-name datacenter-lambda-app \
    --region us-east-1
```

### List Stack Resources

```bash
aws cloudformation describe-stack-resources \
    --stack-name datacenter-lambda-app \
    --region us-east-1
```

### Delete Stack (Optional Cleanup)

Only use this if cleanup is required:

```bash
aws cloudformation delete-stack \
    --stack-name datacenter-lambda-app \
    --region us-east-1
```

---

# Day 48 - Final Result

```text
CloudFormation Stack : datacenter-lambda-app
Stack Status         : CREATE_COMPLETE

IAM Role             : lambda_execution_role

Lambda Function      : datacenter-lambda
Runtime              : Python
Memory               : 128 MB
Timeout              : 10 Seconds

Response Status      : 200
Response Body        : Welcome to KKE AWS Labs!

AWS Region           : us-east-1

Status               : Successfully Completed
```

**Day 48 — Completed Successfully ✅**

100 Days. 100 Challenges. One Cloud Journey. ☁️🚀
