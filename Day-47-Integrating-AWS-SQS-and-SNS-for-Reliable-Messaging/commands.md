# Day 47 - AWS SQS, SNS, Lambda and CloudFormation Commands

This file contains the AWS CLI commands used for **Day 47** of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The task was to deploy a priority-based messaging architecture using AWS CloudFormation, Amazon SQS, Amazon SNS, AWS Lambda, and IAM.

---

# 1. Check AWS CLI Version

First, I checked the installed AWS CLI version:

```bash
aws --version
```

---

# 2. Retrieve AWS Lab Credentials

I retrieved the temporary AWS credentials using:

```bash
showcreds
```

---

# 3. Verify AWS Identity

To verify the active AWS account:

```bash
aws sts get-caller-identity
```

---

# 4. Set the AWS Region

The challenge required all resources to be deployed in:

```text
us-east-1
```

I used the following command to configure the default region:

```bash
aws configure
```

Alternatively, I specified the region directly in AWS CLI commands:

```bash
--region us-east-1
```

---

# 5. Navigate to the CloudFormation Template

The CloudFormation template was located at:

```text
/root/xfusion-priority-stack.yml
```

I navigated to the directory:

```bash
cd /root
```

To list the available files:

```bash
ls -l
```

To inspect the template:

```bash
cat /root/xfusion-priority-stack.yml
```

---

# 6. Validate the CloudFormation Template

Before deploying the stack, the template can be validated using:

```bash
aws cloudformation validate-template \
    --template-body file:///root/xfusion-priority-stack.yml \
    --region us-east-1
```

This checks whether the template is valid CloudFormation syntax.

---

# 7. Create the CloudFormation Stack

The required stack name was:

```text
xfusion-priority-stack
```

The initial stack creation command was:

```bash
aws cloudformation create-stack \
    --stack-name xfusion-priority-stack \
    --template-body file:///root/xfusion-priority-stack.yml \
    --capabilities CAPABILITY_IAM \
    --region us-east-1
```

The stack required IAM resource creation, so the IAM capability was specified.

**Note:** The initial deployment encountered an IAM permission issue because the lab user could not create inline IAM policies.

---

# 8. Check CloudFormation Stack Status

To check the current stack status:

```bash
aws cloudformation describe-stacks \
    --stack-name xfusion-priority-stack \
    --region us-east-1
```

To display only the stack name and status:

```bash
aws cloudformation describe-stacks \
    --stack-name xfusion-priority-stack \
    --region us-east-1 \
    --query 'Stacks[0].[StackName,StackStatus]' \
    --output table
```

---

# 9. Check CloudFormation Stack Events

To investigate deployment errors and resource creation events:

```bash
aws cloudformation describe-stack-events \
    --stack-name xfusion-priority-stack \
    --region us-east-1
```

To display the latest events:

```bash
aws cloudformation describe-stack-events \
    --stack-name xfusion-priority-stack \
    --region us-east-1 \
    --query 'StackEvents[0:15].[Timestamp,LogicalResourceId,ResourceStatus,ResourceStatusReason]' \
    --output table
```

This helped identify the reason for the initial rollback.

---

# 10. Update the CloudFormation Stack

After correcting the template, I updated the existing stack.

```bash
aws cloudformation update-stack \
    --stack-name xfusion-priority-stack \
    --template-body file:///root/xfusion-priority-stack.yml \
    --capabilities CAPABILITY_IAM \
    --region us-east-1
```

The corrected template included:

- AWS managed IAM policies.
- Correct Lambda handler configuration.
- Correct Lambda environment variable names.
- Required priority queue configuration.

To wait for the stack update to finish:

```bash
aws cloudformation wait stack-update-complete \
    --stack-name xfusion-priority-stack \
    --region us-east-1
```

---

# 11. Verify the Final Stack Status

I checked the final stack status:

```bash
aws cloudformation describe-stacks \
    --stack-name xfusion-priority-stack \
    --region us-east-1 \
    --query 'Stacks[0].StackStatus' \
    --output text
```

Expected result:

```text
UPDATE_COMPLETE
```

---

# 12. Verify SQS Queues

To list all SQS queues:

```bash
aws sqs list-queues \
    --region us-east-1
```

To retrieve the high-priority queue URL:

```bash
aws sqs get-queue-url \
    --queue-name xfusion-High-Priority-Queue \
    --region us-east-1
```

To retrieve the low-priority queue URL:

```bash
aws sqs get-queue-url \
    --queue-name xfusion-Low-Priority-Queue \
    --region us-east-1
```

---

# 13. Check SQS Queue Message Counts

To inspect the high-priority queue attributes:

```bash
aws sqs get-queue-attributes \
    --queue-url <HIGH_PRIORITY_QUEUE_URL> \
    --attribute-names ApproximateNumberOfMessages ApproximateNumberOfMessagesNotVisible \
    --region us-east-1
```

To inspect the low-priority queue attributes:

```bash
aws sqs get-queue-attributes \
    --queue-url <LOW_PRIORITY_QUEUE_URL> \
    --attribute-names ApproximateNumberOfMessages ApproximateNumberOfMessagesNotVisible \
    --region us-east-1
```

These attributes help identify the approximate number of available and currently in-flight messages.

---

# 14. Verify the SNS Topic

To list SNS topics:

```bash
aws sns list-topics \
    --region us-east-1
```

To inspect the topic configuration:

```bash
aws sns get-topic-attributes \
    --topic-arn <SNS_TOPIC_ARN> \
    --region us-east-1
```

The required SNS topic was:

```text
xfusion-Priority-Queues-Topic
```

---

# 15. Verify SNS Subscriptions

To list SNS subscriptions:

```bash
aws sns list-subscriptions \
    --region us-east-1
```

To inspect subscriptions for the priority topic:

```bash
aws sns list-subscriptions-by-topic \
    --topic-arn <SNS_TOPIC_ARN> \
    --region us-east-1
```

This helps verify that the SQS queues are subscribed to the SNS topic.

---

# 16. Check SNS Subscription Filter Policies

To inspect the high-priority subscription filter:

```bash
aws sns get-subscription-attributes \
    --subscription-arn <HIGH_PRIORITY_SUBSCRIPTION_ARN> \
    --region us-east-1
```

To inspect the low-priority subscription filter:

```bash
aws sns get-subscription-attributes \
    --subscription-arn <LOW_PRIORITY_SUBSCRIPTION_ARN> \
    --region us-east-1
```

Expected high-priority filter policy:

```json
{
    "priority": ["high"]
}
```

Expected low-priority filter policy:

```json
{
    "priority": ["low"]
}
```

---

# 17. Verify the Lambda Function

To list Lambda functions:

```bash
aws lambda list-functions \
    --region us-east-1
```

To retrieve the required function configuration:

```bash
aws lambda get-function-configuration \
    --function-name xfusion-priorities-queue-function \
    --region us-east-1
```

To display important configuration details:

```bash
aws lambda get-function-configuration \
    --function-name xfusion-priorities-queue-function \
    --region us-east-1 \
    --query '[FunctionName,Handler,MemorySize,Timeout,State]' \
    --output table
```

Expected configuration:

```text
Function Name : xfusion-priorities-queue-function
Handler       : index.lambda_handler
Memory        : 128 MB
Timeout       : 10 seconds
```

---

# 18. Verify Lambda Environment Variables

To inspect Lambda environment variable configuration:

```bash
aws lambda get-function-configuration \
    --function-name xfusion-priorities-queue-function \
    --region us-east-1 \
    --query 'Environment.Variables'
```

The expected variable names were:

```text
high_priority_queue
low_priority_queue
```

**Note:** Avoid sharing command output publicly if it contains sensitive environment variable values.

---

# 19. Verify the IAM Execution Role

To list IAM roles:

```bash
aws iam list-roles \
    --query "Roles[?RoleName=='lambda_execution_role'].RoleName"
```

To inspect the role:

```bash
aws iam get-role \
    --role-name lambda_execution_role
```

To list attached managed policies:

```bash
aws iam list-attached-role-policies \
    --role-name lambda_execution_role
```

The role was configured to use AWS managed policies instead of restricted inline IAM policies.

---

# 20. Retrieve Lambda CloudWatch Logs

The installed AWS CLI was version 1, which did not support:

```bash
aws logs tail
```

Therefore, I used `filter-log-events` instead.

First, I identified the Lambda log group:

```text
/aws/lambda/xfusion-priorities-queue-function
```

Then I retrieved the log events:

```bash
aws logs filter-log-events \
    --log-group-name /aws/lambda/xfusion-priorities-queue-function \
    --region us-east-1
```

To display only the messages:

```bash
aws logs filter-log-events \
    --log-group-name /aws/lambda/xfusion-priorities-queue-function \
    --region us-east-1 \
    --query 'events[*].message' \
    --output text
```

The logs confirmed that the Lambda function executed without errors after the configuration corrections.

---

# 21. Troubleshooting Commands

### Check CloudFormation Events

```bash
aws cloudformation describe-stack-events \
    --stack-name xfusion-priority-stack \
    --region us-east-1
```

### Check Lambda Configuration

```bash
aws lambda get-function-configuration \
    --function-name xfusion-priorities-queue-function \
    --region us-east-1
```

### Check Lambda Logs

```bash
aws logs filter-log-events \
    --log-group-name /aws/lambda/xfusion-priorities-queue-function \
    --region us-east-1
```

### Check SQS Queues

```bash
aws sqs list-queues \
    --region us-east-1
```

### Check SNS Topics

```bash
aws sns list-topics \
    --region us-east-1
```

---

# 22. Important AWS CLI Notes

During this challenge, I learned:

- `aws cloudformation validate-template` validates a CloudFormation template.
- `aws cloudformation create-stack` creates a new stack.
- `aws cloudformation update-stack` updates an existing stack.
- `aws cloudformation describe-stack-events` helps troubleshoot deployment failures.
- `aws sqs get-queue-attributes` checks queue message counts.
- `aws sns get-subscription-attributes` checks subscription filter policies.
- `aws lambda get-function-configuration` verifies Lambda settings.
- `aws logs filter-log-events` retrieves CloudWatch Logs events and works with the installed AWS CLI v1.

---

# Final Verification

The final deployment configuration was:

```text
CloudFormation Stack : xfusion-priority-stack
Region               : us-east-1
High Priority Queue  : xfusion-High-Priority-Queue
Low Priority Queue   : xfusion-Low-Priority-Queue
SNS Topic            : xfusion-Priority-Queues-Topic
Lambda Function      : xfusion-priorities-queue-function
Lambda Memory        : 128 MB
Lambda Timeout       : 10 seconds
Lambda Handler       : index.lambda_handler
IAM Role             : lambda_execution_role
Stack Status         : UPDATE_COMPLETE
```

---

# Day 47 Result

```text
CloudFormation Deployment
          |
          v
     SNS Topic
          |
     +----+----+
     |         |
     v         v
   High       Low
 Priority   Priority
   Queue      Queue
     |         |
     +----+----+
          |
          v
     AWS Lambda
          |
          v
  Priority Processing
          |
          v
   High Queue First
          |
          v
    Low Queue Next
```

**Day 47 — Completed Successfully ✅**

100 Days. 100 Challenges. One Cloud Journey. ☁️🚀
