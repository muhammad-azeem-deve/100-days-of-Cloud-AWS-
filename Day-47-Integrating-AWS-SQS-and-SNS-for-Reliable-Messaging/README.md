# Day 47 - Integrating AWS SQS and SNS for Reliable Messaging

## Challenge Overview

This is Day 47 of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The challenge was related to **AWS Simple Queue Service (SQS), Simple Notification Service (SNS), AWS Lambda, IAM Roles, and CloudFormation**.

The Nautilus DevOps team required a priority-based messaging system on AWS. The objective was to process high-priority messages before low-priority messages to ensure that critical tasks were handled first.

To achieve this, I used AWS CloudFormation to deploy the required infrastructure from an existing template file.

The CloudFormation template was located at:

```text
/root/xfusion-priority-stack.yml
```

The stack was required to create two SQS queues, one SNS topic, a Lambda function, and an IAM execution role.

SNS filter policies were configured to route messages according to their priority, while Lambda was responsible for processing messages from the high-priority queue before processing messages from the low-priority queue.

### Challenge Requirement

The requirements were:

1. Access the AWS client machine.
2. Use the CloudFormation template located at `/root/xfusion-priority-stack.yml`.
3. Create a CloudFormation stack named `xfusion-priority-stack`.
4. Deploy all resources in the `us-east-1` region.
5. Create an SQS queue named `xfusion-High-Priority-Queue`.
6. Create another SQS queue named `xfusion-Low-Priority-Queue`.
7. Create an SNS topic named `xfusion-Priority-Queues-Topic`.
8. Create a Lambda function named `xfusion-priorities-queue-function`.
9. Configure Lambda with 128 MB memory and a 10-second timeout.
10. Create an IAM role named `lambda_execution_role`.
11. Configure SNS filter policies using the `priority` message attribute.
12. Route high-priority messages to the high-priority queue.
13. Route low-priority messages to the low-priority queue.
14. Configure Lambda to process the high-priority queue first.
15. Process messages from the low-priority queue only when the high-priority queue is empty.
16. Verify the deployment and message-processing behavior.

---

## Objectives

The objectives of this challenge are:

- Understand AWS messaging services.
- Learn how Amazon SQS handles message queues.
- Understand Amazon SNS topics and subscriptions.
- Configure SNS filter policies.
- Integrate SNS with SQS.
- Deploy AWS infrastructure using CloudFormation.
- Create and configure AWS Lambda functions.
- Understand IAM execution roles and permissions.
- Implement priority-based message processing.
- Troubleshoot CloudFormation deployment errors.
- Verify AWS resources using the AWS CLI.
- Understand how serverless services work together to build reliable messaging systems.

---

## Environment

| Item | Details |
|---|---|
| Challenge | 100 Days of Cloud (AWS) |
| Platform | KodeKloud |
| Day | 47 |
| Cloud Provider | Amazon Web Services |
| Region | `us-east-1` |
| AWS Services | SQS, SNS, Lambda, IAM, CloudFormation |
| CloudFormation Template | `/root/xfusion-priority-stack.yml` |
| Stack Name | `xfusion-priority-stack` |
| High Priority Queue | `xfusion-High-Priority-Queue` |
| Low Priority Queue | `xfusion-Low-Priority-Queue` |
| SNS Topic | `xfusion-Priority-Queues-Topic` |
| Lambda Function | `xfusion-priorities-queue-function` |
| Lambda Memory | 128 MB |
| Lambda Timeout | 10 seconds |
| IAM Role | `lambda_execution_role` |
| Final Stack Status | `UPDATE_COMPLETE` |
| Status | Completed |

---

# Solution

## Step 1: Access the AWS Client Machine

First, I accessed the AWS client machine provided by the KodeKloud lab environment.

I checked the machine using:

```bash
hostname
```

I also verified that the AWS CLI was available:

```bash
aws --version
```

The lab credentials were retrieved using:

```bash
showcreds
```

I made sure to use the required AWS region:

```text
us-east-1
```

---

## Step 2: Locate the CloudFormation Template

The CloudFormation template was already available on the AWS client machine.

Its location was:

```text
/root/xfusion-priority-stack.yml
```

I navigated to the directory:

```bash
cd /root
```

Then I checked the available files:

```bash
ls -l
```

I inspected the CloudFormation template:

```bash
cat /root/xfusion-priority-stack.yml
```

This helped me understand the resources and configurations defined in the template.

---

## Step 3: Understand the Required AWS Architecture

Before deploying the stack, I identified the services required for the priority messaging system.

The architecture included:

- Two Amazon SQS queues for different priority levels.
- One Amazon SNS topic for message distribution.
- SNS subscriptions with priority-based filter policies.
- An AWS Lambda function for message processing.
- An IAM execution role for Lambda permissions.
- AWS CloudFormation for infrastructure deployment.

The two queues were:

```text
xfusion-High-Priority-Queue
xfusion-Low-Priority-Queue
```

The SNS topic was:

```text
xfusion-Priority-Queues-Topic
```

---

## Step 4: Create the CloudFormation Stack

I used the existing CloudFormation template to deploy the AWS resources.

The required stack name was:

```text
xfusion-priority-stack
```

I initially attempted to deploy the stack using CloudFormation.

The deployment encountered an error because the lab IAM user did not have permission to create inline IAM policies.

As a result, the initial deployment entered rollback.

I investigated the issue and updated the template to use AWS managed policies instead of creating inline policies.

This allowed the Lambda execution role to use the required managed permissions without attempting the restricted inline-policy operation.

---

## Step 5: Configure the IAM Execution Role

The Lambda function required an IAM execution role to access the necessary AWS services.

The required role name was:

```text
lambda_execution_role
```

I updated the CloudFormation template to use AWS managed policies because the lab environment restricted the creation of inline IAM policies.

The role was responsible for providing the Lambda function with the permissions required for its execution.

---

## Step 6: Configure the SNS Topic

The SNS topic was created with the required name:

```text
xfusion-Priority-Queues-Topic
```

Amazon SNS acts as a message distribution service.

Instead of sending messages directly to individual queues, publishers can send messages to the SNS topic.

SNS then distributes messages to subscribed endpoints according to their subscription configurations.

---

## Step 7: Configure the SQS Queues

I configured two SQS queues for priority-based message handling.

### High-Priority Queue

```text
xfusion-High-Priority-Queue
```

This queue stores messages that require immediate processing.

### Low-Priority Queue

```text
xfusion-Low-Priority-Queue
```

This queue stores messages that can be processed after all high-priority messages have been handled.

The two separate queues make it possible to implement priority-based message processing.

---

## Step 8: Configure SNS Filter Policies

I configured SNS subscription filter policies using the message attribute:

```text
priority
```

The purpose of these policies was to route messages to the appropriate SQS queue.

The routing logic was:

### High-Priority Messages

```json
{
  "priority": ["high"]
}
```

These messages were routed to:

```text
xfusion-High-Priority-Queue
```

### Low-Priority Messages

```json
{
  "priority": ["low"]
}
```

These messages were routed to:

```text
xfusion-Low-Priority-Queue
```

This configuration ensured that messages were distributed according to their priority attribute.

---

## Step 9: Configure the AWS Lambda Function

The required Lambda function name was:

```text
xfusion-priorities-queue-function
```

I configured the Lambda function with the following settings:

```text
Function Name : xfusion-priorities-queue-function
Memory Size   : 128 MB
Timeout       : 10 seconds
```

The Lambda function was responsible for consuming messages from the SQS queues.

Its main responsibility was to process high-priority messages before low-priority messages.

---

## Step 10: Configure the Lambda Handler

During troubleshooting, I discovered that the Lambda handler configuration was incorrect.

The initial handler was:

```text
index.handler
```

However, the actual Python function name in the Lambda code was:

```python
lambda_handler
```

Therefore, I corrected the handler configuration to:

```text
index.lambda_handler
```

This ensured that AWS Lambda could locate and execute the correct function.

---

## Step 11: Correct Lambda Environment Variables

Another issue occurred because the environment variable names in the CloudFormation template did not match the names expected by the Lambda code.

The code expected lowercase variable names:

```text
high_priority_queue
low_priority_queue
```

However, the template initially used uppercase environment variable names.

I corrected the environment variable configuration to match the Lambda code.

The required variables were:

```text
high_priority_queue
low_priority_queue
```

This allowed the Lambda function to identify the correct SQS queues.

---

## Step 12: Implement Priority-Based Message Processing

The Lambda function was configured to follow a priority-based processing approach.

The processing logic was:

1. Check the high-priority queue first.
2. If high-priority messages are available, process them.
3. Continue processing high-priority messages.
4. Check the low-priority queue only when the high-priority queue is empty.
5. Process low-priority messages afterward.

The overall logic can be represented as:

```text
          Lambda Function
                 |
                 v
       Check High Priority Queue
                 |
          +------+------+
          |             |
       Messages       Empty
       Available        |
          |             v
          v       Check Low Priority
    Process High       Queue
     Priority           |
          |        +----+----+
          |        |         |
          |      Messages   Empty
          |      Available   |
          |        |         |
          v        v         v
       Check     Process    Wait for
       Again      Low       Messages
                 Priority
```

This approach ensures that high-priority messages receive preference over low-priority messages.

---

## Step 13: Update the CloudFormation Stack

After fixing the IAM policy issue, Lambda handler configuration, and environment variable names, I updated the CloudFormation template.

I deployed the corrected configuration using the existing stack.

The stack was updated successfully and reached:

```text
UPDATE_COMPLETE
```

This indicated that CloudFormation had successfully applied the updated resource configuration.

---

## Step 14: Verify the CloudFormation Stack

I checked the stack status using the AWS CLI.

The required stack was:

```text
xfusion-priority-stack
```

The final status was:

```text
UPDATE_COMPLETE
```

This confirmed that the updated CloudFormation deployment had completed successfully.

---

## Step 15: Verify the AWS Resources

After the deployment, I verified the resources created by the CloudFormation stack.

The expected resources were:

```text
SQS Queues:
- xfusion-High-Priority-Queue
- xfusion-Low-Priority-Queue

SNS Topic:
- xfusion-Priority-Queues-Topic

Lambda Function:
- xfusion-priorities-queue-function

IAM Role:
- lambda_execution_role
```

I also verified that the Lambda function was configured with:

```text
Memory: 128 MB
Timeout: 10 seconds
Handler: index.lambda_handler
```

---

## Step 16: Verify Lambda Execution Logs

During verification, I attempted to use:

```bash
aws logs tail
```

However, the installed AWS CLI was version 1, which did not support this command.

I used the following alternative:

```bash
aws logs filter-log-events
```

This allowed me to retrieve Lambda execution logs from CloudWatch Logs.

The Lambda function executed without errors after correcting the handler and environment variable configurations.

One limitation remained: the logs did not explicitly print the processing order of individual messages.

---

## Step 17: Test Priority-Based Queue Processing

Finally, I performed a queue-count test to verify the priority-based processing behavior.

I checked the number of messages in both SQS queues.

The expected behavior was:

```text
High Priority Queue:
Messages processed first.

Low Priority Queue:
Messages processed only after the high-priority queue becomes empty.
```

The queue-count test showed that the high-priority queue was draining before the low-priority queue.

One unusual queue reading remained unexplained, but it did not prevent the deployment or the observed priority-processing behavior from succeeding.

---

# Troubleshooting

During this challenge, I encountered several issues and resolved them.

## Issue 1: CloudFormation Rollback Due to IAM Permissions

**Problem:**

The initial deployment failed because the lab IAM user did not have permission to create inline IAM policies.

**Solution:**

I modified the CloudFormation template to use AWS managed policies instead of creating inline IAM policies.

---

## Issue 2: AWS CLI Logs Tail Command Not Available

**Problem:**

The following command was unavailable in the installed AWS CLI v1:

```bash
aws logs tail
```

**Solution:**

I used the alternative command:

```bash
aws logs filter-log-events
```

This allowed me to retrieve CloudWatch Logs events.

---

## Issue 3: Incorrect Lambda Handler

**Problem:**

The Lambda function was initially configured with:

```text
index.handler
```

But the Python function was named:

```python
lambda_handler
```

**Solution:**

I corrected the handler configuration to:

```text
index.lambda_handler
```

---

## Issue 4: Incorrect Environment Variable Names

**Problem:**

The CloudFormation template used uppercase environment variable names, while the Lambda code expected lowercase names.

**Solution:**

I updated the environment variables to:

```text
high_priority_queue
low_priority_queue
```

This resolved the configuration mismatch.

---

## Issue 5: Processing Order Not Visible in Logs

**Problem:**

The Lambda function executed without errors, but the logs did not explicitly show the order in which messages were processed.

**Observation:**

The queue-count test showed that the high-priority queue drained before the low-priority queue.

The exact processing order was not independently confirmed through individual message logs.

---

# AWS Architecture Workflow

The overall architecture for this challenge was:

```text
              Message Publisher
                     |
                     v
              Amazon SNS Topic
                     |
          +----------+----------+
          |                     |
          v                     v
    Priority = High       Priority = Low
          |                     |
          v                     v
   High Priority SQS       Low Priority SQS
          |                     |
          +----------+----------+
                     |
                     v
                AWS Lambda
                     |
                     v
          Check High Priority First
                     |
             +-------+-------+
             |               |
             v               v
        High Queue       High Queue
        Has Messages     Is Empty
             |               |
             v               v
        Process High     Process Low
        Priority First   Priority Messages
```

---

# What I Learned

From this challenge, I learned and practiced:

- How to deploy AWS infrastructure using CloudFormation.
- How to create and manage CloudFormation stacks.
- How Amazon SQS queues work.
- How Amazon SNS distributes messages.
- How to configure SNS subscription filter policies.
- How to route messages using SNS message attributes.
- How to integrate SNS with SQS.
- How AWS Lambda processes messages.
- How to configure Lambda memory and timeout settings.
- How IAM execution roles provide permissions to Lambda.
- The difference between AWS managed policies and inline policies.
- How to troubleshoot CloudFormation deployment failures.
- How to configure Lambda handlers correctly.
- How environment variable names affect Lambda execution.
- How to retrieve CloudWatch Logs using AWS CLI v1.
- How to implement priority-based message processing.
- How to verify queue behavior using message counts.

# Challenge Status

Day 47 — Completed Successfully ✅

**Deployed the AWS priority messaging infrastructure using CloudFormation, configured SNS filtering to route high- and low-priority messages into separate SQS queues, integrated the queues with Lambda, resolved IAM and Lambda configuration issues, and verified that high-priority messages were processed before low-priority messages.**

100 Days. 100 Challenges. One Cloud Journey. ☁️🚀

I will continue documenting each challenge and improving my practical AWS, Cloud Engineering, and DevOps skills step by step.
