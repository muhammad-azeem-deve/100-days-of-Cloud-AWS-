# Day 36 - AWS Application Load Balancer Verification Commands

This file contains the AWS CLI commands used to verify the resources created during **Day 36** of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

All resources were created using the **AWS Management Console**. The AWS CLI was used only for verification.

---

## 1. Verify AWS CLI

Check whether AWS CLI is installed:

```bash
aws --version
```

---

## 2. Verify AWS Identity

Check the currently authenticated AWS account:

```bash
aws sts get-caller-identity
```

---

## 3. Verify AWS Region

Check the configured AWS region:

```bash
aws configure get region
```

Required region:

```text
us-east-1
```

---

## 4. Verify EC2 Instance

Check whether the `xfusion-ec2` instance exists and is running:

```bash
aws ec2 describe-instances \
    --filters "Name=tag:Name,Values=xfusion-ec2" \
    --region us-east-1 \
    --query 'Reservations[*].Instances[*].[InstanceId,Tags[?Key==`Name`]|[0].Value,State.Name,InstanceType,PrivateIpAddress]' \
    --output table
```

Expected information:

```text
Instance Name : xfusion-ec2
State         : running
```

---

## 5. Verify EC2 Instance Status Checks

Check the EC2 instance health:

```bash
aws ec2 describe-instance-status \
    --filters "Name=tag:Name,Values=xfusion-ec2" \
    --include-all-instances \
    --region us-east-1 \
    --query 'InstanceStatuses[*].[InstanceId,InstanceState.Name,InstanceStatus.Status,SystemStatus.Status]' \
    --output table
```

Expected status:

```text
Instance State  : running
Instance Status : ok
System Status   : ok
```

---

## 6. Verify EC2 Security Group

Check whether the `xfusion-sg` security group exists:

```bash
aws ec2 describe-security-groups \
    --filters "Name=group-name,Values=xfusion-sg" \
    --region us-east-1 \
    --query 'SecurityGroups[*].[GroupId,GroupName,Description]' \
    --output table
```

---

## 7. Verify Security Group Inbound Rules

Check the inbound rules of `xfusion-sg`:

```bash
aws ec2 describe-security-groups \
    --filters "Name=group-name,Values=xfusion-sg" \
    --region us-east-1 \
    --query 'SecurityGroups[*].IpPermissions' \
    --output json
```

Verify that HTTP traffic is allowed on port `80` from the ALB security group.

---

## 8. Verify Application Load Balancer

Check whether the Application Load Balancer exists:

```bash
aws elbv2 describe-load-balancers \
    --names xfusion-alb \
    --region us-east-1 \
    --query 'LoadBalancers[*].[LoadBalancerName,State.Code,Scheme,Type,DNSName,VpcId]' \
    --output table
```

Expected information:

```text
Load Balancer : xfusion-alb
State         : active
Scheme        : internet-facing
Type          : application
```

---

## 9. Verify Target Group

Check whether the target group exists:

```bash
aws elbv2 describe-target-groups \
    --names xfusion-tg \
    --region us-east-1 \
    --query 'TargetGroups[*].[TargetGroupName,Protocol,Port,HealthCheckProtocol,HealthCheckPath,VpcId]' \
    --output table
```

Expected information:

```text
Target Group Name : xfusion-tg
Protocol          : HTTP
Port              : 80
Health Check      : HTTP
Health Check Path : /
```

---

## 10. Verify Registered Targets

First, retrieve the target group ARN:

```bash
TG_ARN=$(aws elbv2 describe-target-groups \
    --names xfusion-tg \
    --region us-east-1 \
    --query 'TargetGroups[0].TargetGroupArn' \
    --output text)
```

Then check the registered EC2 instances:

```bash
aws elbv2 describe-target-health \
    --target-group-arn "$TG_ARN" \
    --region us-east-1 \
    --query 'TargetHealthDescriptions[*].[Target.Id,Target.Port,TargetHealth.State,TargetHealth.Description]' \
    --output table
```

Expected information:

```text
Target Port : 80
Health      : healthy
```

---

## 11. Verify ALB Listener

Check the listener configuration:

```bash
aws elbv2 describe-listeners \
    --load-balancer-arn "$(aws elbv2 describe-load-balancers \
    --names xfusion-alb \
    --region us-east-1 \
    --query 'LoadBalancers[0].LoadBalancerArn' \
    --output text)" \
    --region us-east-1 \
    --query 'Listeners[*].[Protocol,Port,DefaultActions[0].Type,DefaultActions[0].TargetGroupArn]' \
    --output table
```

Expected information:

```text
Protocol : HTTP
Port     : 80
Action   : forward
```

---

## 12. Verify ALB Security Group

Display the security groups attached to the ALB:

```bash
aws elbv2 describe-load-balancers \
    --names xfusion-alb \
    --region us-east-1 \
    --query 'LoadBalancers[0].SecurityGroups' \
    --output table
```

Verify the inbound rules of the attached security group:

```bash
aws ec2 describe-security-groups \
    --group-ids <ALB-SECURITY-GROUP-ID> \
    --region us-east-1 \
    --query 'SecurityGroups[*].IpPermissions' \
    --output json
```

Verify that HTTP traffic on port `80` is allowed from `0.0.0.0/0`.

---

## 13. Verify ALB DNS Name

Retrieve the DNS name of the Application Load Balancer:

```bash
aws elbv2 describe-load-balancers \
    --names xfusion-alb \
    --region us-east-1 \
    --query 'LoadBalancers[0].DNSName' \
    --output text
```

---

## 14. Test Application Through ALB DNS

Retrieve the ALB DNS name and send an HTTP request:

```bash
ALB_DNS=$(aws elbv2 describe-load-balancers \
    --names xfusion-alb \
    --region us-east-1 \
    --query 'LoadBalancers[0].DNSName' \
    --output text)

curl -I "http://$ALB_DNS"
```

Expected HTTP response:

```text
HTTP/1.1 200 OK
```

To display the Nginx welcome page HTML:

```bash
curl "http://$ALB_DNS"
```

---

## 15. Final Verification Summary

| Resource | Expected Result |
|---|---|
| EC2 Instance | Running |
| EC2 Status Checks | 2/2 Passed |
| Security Group | xfusion-sg |
| Target Group | xfusion-tg |
| Target Health | Healthy |
| Application Load Balancer | Active |
| Listener | HTTP : 80 |
| Forwarding Action | xfusion-tg |
| ALB DNS | Accessible |
| Nginx Web Server | HTTP 200 OK |

---

## Day 36 Result

```text
EC2 Instance       : xfusion-ec2
Security Group     : xfusion-sg
Target Group       : xfusion-tg
Load Balancer      : xfusion-alb
Listener           : HTTP : 80
Target Port        : 80
Target Health      : Healthy
Application Access : Successful
Status             : Completed
```

**Day 36 — Completed Successfully ✅**

All AWS resources were created through the AWS Management Console and verified using AWS CLI.
