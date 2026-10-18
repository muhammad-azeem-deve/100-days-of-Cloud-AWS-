# Day 25 - AWS EC2 CloudWatch CPU Monitoring with SNS Alerts

## Challenge Overview

This is Day 25 of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The challenge was related to **Amazon EC2, Amazon CloudWatch Monitoring, CloudWatch Alarms, and Amazon SNS Notifications**.

The task was to set up an EC2 instance and configure Amazon CloudWatch to monitor its CPU utilization. An alarm had to be configured to trigger when the average CPU utilization reached or exceeded 90% over a five-minute monitoring period.

Amazon SNS was used as the notification service to send an alert when the CloudWatch alarm entered the alarm state.

### Challenge Requirement

The requirements were:

1. Create or configure an EC2 instance using the AWS Management Console.
2. Ensure that the EC2 instance is running in the `us-east-1` region.
3. Use Amazon CloudWatch to monitor the instance's CPU utilization.
4. Create a CloudWatch alarm with a CPU utilization threshold of 90%.
5. Configure the statistic as `Average`.
6. Configure the monitoring period as 5 minutes.
7. Configure the alarm to trigger when CPU utilization is greater than or equal to 90%.
8. Create or select an Amazon SNS topic for notifications.
9. Subscribe an email address to the SNS topic and confirm the subscription.
10. Verify the EC2 instance, CloudWatch alarm, and SNS notification configuration using the AWS Console and AWS CLI.

---

## Objectives

The objectives of this challenge are:

- Understand Amazon EC2 instance monitoring.
- Learn how CloudWatch collects EC2 CPU utilization metrics.
- Configure a CloudWatch alarm.
- Understand the `Average` statistic.
- Configure a CPU utilization threshold of 90%.
- Set a five-minute monitoring period.
- Integrate CloudWatch alarms with Amazon SNS.
- Understand email subscription confirmation.
- Verify AWS monitoring resources using the Console and CLI.

---

## Environment

| Item | Details |
|---|---|
| Challenge | 100 Days of Cloud (AWS) |
| Platform | KodeKloud |
| Day | 25 |
| Cloud Provider | Amazon Web Services |
| Compute Service | Amazon EC2 |
| Monitoring Service | Amazon CloudWatch |
| Notification Service | Amazon SNS |
| AWS Region | `us-east-1` |
| Metric | `CPUUtilization` |
| Statistic | `Average` |
| Threshold | 90% |
| Comparison Operator | Greater than or equal to |
| Period | 5 minutes (300 seconds) |
| Evaluation Periods | 1 |
| Alarm Action | Amazon SNS notification |
| Status | Completed |

---

# Solution

## Step 1: Access the AWS Management Console

First, I logged into the AWS Management Console using the credentials provided by the KodeKloud lab environment.

I selected the required AWS region:

```text
us-east-1
```

All resources for this challenge were configured in this region.

---

## Step 2: Open Amazon EC2

From the AWS Management Console, I opened the EC2 service.

Navigation:

```text
AWS Management Console
        |
        v
       EC2
        |
        v
   Instances
```

I checked whether an EC2 instance was already available in the lab.

If no suitable instance existed, I launched one using the lab's available configuration and verified that it reached the `Running` state.

I recorded the instance ID for use in the CloudWatch alarm configuration.

---

## Step 3: Verify the EC2 Instance

I opened the EC2 Instances page and checked the instance details.

The important information was:

```text
Instance ID : <your-instance-id>
Instance State : Running
Region : us-east-1
```

I used the instance ID to identify the correct EC2 resource when configuring CloudWatch.

---

## Step 4: Open Amazon CloudWatch

Next, I opened Amazon CloudWatch from the AWS Management Console.

Navigation:

```text
AWS Management Console
        |
        v
    CloudWatch
        |
        v
      Alarms
        |
        v
   Create alarm
```

CloudWatch allows AWS users to monitor metrics and create alarms when a metric meets a specified condition.

---

## Step 5: Select the EC2 CPU Utilization Metric

During alarm creation, I selected the EC2 metric for the instance.

The metric configuration was:

```text
Namespace: AWS/EC2
Metric: CPUUtilization
Dimension: InstanceId
```

I selected the `CPUUtilization` metric corresponding to my EC2 instance.

This metric represents the percentage of allocated EC2 compute capacity being used by the instance.

---

## Step 6: Configure the Alarm Conditions

I configured the CloudWatch alarm using the required threshold and statistic.

The alarm settings were:

```text
Statistic: Average
Period: 5 minutes
Threshold type: Static
Condition: Greater than or equal to
Threshold value: 90
```

The corresponding comparison was:

```text
Average CPU utilization >= 90%
```

A five-minute period is equivalent to 300 seconds.

With one evaluation period, the alarm evaluates the average CPU utilization for a five-minute interval and enters the alarm state if the threshold condition is met.

> Note: CloudWatch evaluates alarm conditions using metric data points. Missing data, evaluation timing, and metric publication can affect when an alarm changes state.

---

## Step 7: Configure the Alarm Name

I provided a descriptive name for the CloudWatch alarm.

Example:

```text
Alarm Name: EC2-High-CPU-Alarm
```

The alarm name is an example and can be replaced with the actual name used in the lab.

I reviewed the metric and threshold settings before continuing.

---

## Step 8: Create an Amazon SNS Topic

To receive notifications, I opened Amazon Simple Notification Service (SNS).

Navigation:

```text
AWS Management Console
        |
        v
       SNS
        |
        v
      Topics
        |
        v
    Create topic
```

I created or selected an SNS topic for the CloudWatch alarm.

Example topic name:

```text
ec2-high-cpu-alerts
```

I used a standard SNS topic because it supports email notifications for CloudWatch alarms.

---

## Step 9: Create an Email Subscription

Inside the SNS topic, I created an email subscription.

The subscription configuration was:

```text
Protocol: Email
Endpoint: <your-email-address>
```

I then checked the email inbox and confirmed the subscription using the link in the SNS confirmation email.

The subscription must be confirmed before SNS can deliver notifications to that email endpoint.

---

## Step 10: Connect the CloudWatch Alarm to SNS

I returned to the CloudWatch alarm configuration.

Under the alarm notification or alarm actions section, I selected the SNS topic created earlier.

The notification configuration was:

```text
Alarm State: In alarm
Notification Service: Amazon SNS
SNS Topic: ec2-high-cpu-alerts
```

I configured the SNS notification action for the alarm state.

I then reviewed the configuration and created the alarm.

---

## Step 11: Verify the CloudWatch Alarm

After creating the alarm, I opened the CloudWatch Alarms page.

I checked the following settings:

```text
Metric: CPUUtilization
Statistic: Average
Period: 5 minutes
Threshold: >= 90%
Alarm Action: SNS notification
```

I also checked the alarm state.

Possible states include:

- `OK` — the metric does not currently breach the threshold.
- `ALARM` — the threshold condition has been met.
- `INSUFFICIENT_DATA` — CloudWatch does not have enough usable data to determine the state.

An alarm in the `OK` state can still be configured correctly. The CPU does not have to reach 90% for the alarm configuration to be valid.

---

## Step 12: Verify the SNS Topic and Subscription

I opened the SNS topic in the AWS Console and checked:

- The topic exists.
- The CloudWatch alarm is configured to use the topic.
- The email subscription exists.
- The email subscription status is `Confirmed`.

A pending subscription will not deliver email notifications until the recipient confirms it.

---

## Step 13: Verify Using AWS CLI

After completing the setup in the AWS Console, I used AWS CLI commands to verify the resources.

The verification commands are documented separately in `commands.md`.

The main resources checked were:

```text
EC2 Instance
CloudWatch Alarm
CloudWatch Alarm Configuration
SNS Topic
SNS Email Subscription
```

The AWS CLI commands explicitly use the `us-east-1` region.

---

# Monitoring Workflow

The overall workflow for this challenge was:

```text
EC2 Instance
      |
      v
CPUUtilization Metric
      |
      v
Amazon CloudWatch
      |
      v
Average CPU >= 90%
during a 5-minute period
      |
      v
CloudWatch Alarm
      |
      v
Amazon SNS Topic
      |
      v
Confirmed Email Subscription
      |
      v
Email Notification
```

---

# Important Notes

- EC2 CPU utilization metrics are available through CloudWatch basic monitoring, which commonly publishes metrics at five-minute intervals.
- The `Average` statistic calculates the average of the reported metric samples within the selected period.
- The alarm threshold is configured at 90%.
- The five-minute period is 300 seconds.
- The SNS email subscription must be confirmed.
- An alarm entering the `ALARM` state does not necessarily mean the email was delivered; SNS delivery should be verified separately if required.
- Actual alarm notifications are generated when the alarm changes state into `ALARM`. Repeated evaluations while it remains in `ALARM` do not necessarily produce another notification.
- CPU utilization does not automatically reach 90%. A controlled load test may be needed to test the notification end to end.
- Never publish AWS access keys, secret keys, session tokens, passwords, or private key files to GitHub.

---

# What I Learned

From this challenge, I learned and practiced:

- How to configure an EC2 instance.
- How to monitor EC2 CPU utilization with CloudWatch.
- How to select the `CPUUtilization` metric.
- How the `Average` statistic works.
- How to configure a five-minute alarm period.
- How to set a CPU utilization threshold of 90%.
- How to create and configure CloudWatch alarms.
- How to create an SNS topic.
- How to subscribe and confirm an email endpoint.
- How to connect SNS notifications to a CloudWatch alarm.
- How to verify AWS resources using the Console and CLI.
- How AWS monitoring and notification services work together.

# Challenge Status

Day 25 — Completed Successfully 

**Configured EC2 CPU monitoring with Amazon CloudWatch using the `Average` statistic, a 90% CPU threshold, and a five-minute period, and connected the alarm to Amazon SNS for email notifications.**

100 Days. 100 Challenges. One Cloud Journey. 

I will continue documenting each challenge and building my practical AWS cloud skills step by step.
