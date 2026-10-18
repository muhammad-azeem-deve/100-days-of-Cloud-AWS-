# Day 25 - AWS CloudWatch and SNS Verification Commands

This file contains AWS CLI commands to verify the EC2 instance, CloudWatch CPU alarm, alarm configuration, SNS topic, and email subscription for Day 25 of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

All commands use the `us-east-1` region.

## 1. Verify AWS CLI and Identity

Check the installed AWS CLI version:

```bash
aws --version
```

Verify the active AWS identity:

```bash
aws sts get-caller-identity
```

---

## 2. Verify the EC2 Instance

List EC2 instances and their states:

```bash
aws ec2 describe-instances \
  --region us-east-1 \
  --query 'Reservations[].Instances[].[InstanceId,State.Name,InstanceType]' \
  --output table
```

Verify a specific instance:

```bash
aws ec2 describe-instances \
  --instance-ids <instance-id> \
  --region us-east-1 \
  --query 'Reservations[].Instances[].[InstanceId,State.Name,Placement.AvailabilityZone]' \
  --output table
```

Replace `<instance-id>` with the actual EC2 instance ID.

---

## 3. Verify the CloudWatch Alarm

List CloudWatch alarms:

```bash
aws cloudwatch describe-alarms \
  --region us-east-1 \
  --query 'MetricAlarms[].[AlarmName,StateValue,MetricName,Namespace]' \
  --output table
```

Inspect the specific alarm:

```bash
aws cloudwatch describe-alarms \
  --alarm-names EC2-High-CPU-Alarm \
  --region us-east-1
```

Replace `EC2-High-CPU-Alarm` with the actual alarm name.

---

## 4. Verify the Alarm Threshold, Statistic, and Period

Display the required alarm configuration:

```bash
aws cloudwatch describe-alarms \
  --alarm-names EC2-High-CPU-Alarm \
  --region us-east-1 \
  --query 'MetricAlarms[0].[AlarmName,MetricName,Namespace,Statistic,Period,EvaluationPeriods,Threshold,ComparisonOperator,StateValue]' \
  --output table
```

Expected configuration:

```text
MetricName: CPUUtilization
Namespace: AWS/EC2
Statistic: Average
Period: 300
EvaluationPeriods: 1
Threshold: 90
ComparisonOperator: GreaterThanOrEqualToThreshold
```

The alarm's current state may be `OK`, `ALARM`, or `INSUFFICIENT_DATA`.

---


## 11. Verify SNS Delivery Status (Optional)

If SNS delivery logging is enabled and configured for the topic, delivery logs can be inspected in the configured destination, such as CloudWatch Logs.

SNS topic attributes and subscription status alone do not prove that a specific email was successfully delivered.

Check the recipient's inbox and spam folder to confirm actual email receipt.

---

## Final Verification Checklist

- [ ] AWS CLI identity verified.
- [ ] EC2 instance exists and is running.
- [ ] CloudWatch alarm exists.
- [ ] Metric is `CPUUtilization`.
- [ ] Namespace is `AWS/EC2`.
- [ ] Statistic is `Average`.
- [ ] Period is `300` seconds.
- [ ] Threshold is `90`.
- [ ] Comparison operator is `GreaterThanOrEqualToThreshold`.
- [ ] Alarm action contains the intended SNS topic ARN.
- [ ] SNS topic exists in `us-east-1`.
- [ ] Email subscription status is `Confirmed`.
- [ ] CPU metrics and alarm history have been inspected.

**Day 25 — AWS CloudWatch and SNS Verification Completed.**
