# Day 44 - AWS Auto Scaling and High Availability Commands

This file contains the AWS CLI commands used to create, configure, and verify the infrastructure for **Day 44** of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The task was to create a highly available web application using EC2 Launch Templates, Auto Scaling Groups, Target Groups, and an Application Load Balancer.

---

# 1. Verify AWS CLI

First, I checked whether the AWS CLI was installed:

```bash
aws --version
```

---

# 2. Verify AWS Credentials

I retrieved the temporary credentials provided by KodeKloud:

```bash
showcreds
```

I verified the active AWS identity:

```bash
aws sts get-caller-identity
```

---

# 3. Configure AWS Region

The required AWS region was:

```text
us-east-1
```

To configure the AWS CLI:

```bash
aws configure
```

Alternatively, the region can be specified directly in each AWS CLI command:

```bash
--region us-east-1
```

---

# 4. Find Amazon Linux 2023 AMI

To find the latest Amazon Linux 2023 AMI:

```bash
aws ec2 describe-images \
    --owners amazon \
    --filters \
    "Name=name,Values=al2023-ami-2023.*-x86_64" \
    "Name=state,Values=available" \
    --region us-east-1 \
    --query 'Images | sort_by(@, &CreationDate)[-1].[ImageId,Name]' \
    --output table
```

The returned AMI ID can be used when creating the Launch Template.

---

# 5. Create the EC2 Launch Template

The required Launch Template configuration was:

```text
Name: devops-launch-template
AMI: Amazon Linux 2023
Instance Type: t2.micro
```

The Launch Template can be created using:

```bash
aws ec2 create-launch-template \
    --launch-template-name devops-launch-template \
    --version-description "Day 44 AWS Auto Scaling" \
    --launch-template-data '{
        "ImageId": "ami-xxxxxxxxxxxxxxxxx",
        "InstanceType": "t2.micro",
        "SecurityGroupIds": ["sg-xxxxxxxxxxxxxxxxx"],
        "UserData": "IyEvYmluL2Jhc2gKZG5mIHVwZGF0ZSAt eQ=="
    }' \
    --region us-east-1
```

Replace the example AMI ID and security group ID with the actual resource IDs.

The User Data must contain Base64-encoded script content. For example, generate it using:

```bash
base64 -w 0 user-data.sh
```

The actual `user-data.sh` file:

```bash
#!/bin/bash

dnf update -y

dnf install nginx -y

systemctl start nginx

systemctl enable nginx
```

---

# 6. Verify the Launch Template

To verify the Launch Template:

```bash
aws ec2 describe-launch-templates \
    --launch-template-names devops-launch-template \
    --region us-east-1
```

To check its configuration:

```bash
aws ec2 describe-launch-template-versions \
    --launch-template-name devops-launch-template \
    --versions '$Latest' \
    --region us-east-1
```

---

# 7. Create the Auto Scaling Group

The Auto Scaling Group configuration was:

```text
Name: devops-asg
Minimum Capacity: 1
Desired Capacity: 1
Maximum Capacity: 2
```

The CLI command is:

```bash
aws autoscaling create-auto-scaling-group \
    --auto-scaling-group-name devops-asg \
    --launch-template LaunchTemplateName=devops-launch-template,Version='$Latest' \
    --min-size 1 \
    --desired-capacity 1 \
    --max-size 2 \
    --vpc-zone-identifier "subnet-xxxxxxxx,subnet-yyyyyyyy" \
    --health-check-type ELB \
    --health-check-grace-period 300 \
    --region us-east-1
```

Replace the example subnet IDs with the actual subnet IDs.

For an internet-facing ALB, the selected subnets should be in suitable Availability Zones and have the necessary internet connectivity.

---

# 8. Configure Target Tracking Scaling Policy

The scaling policy was configured to maintain average CPU utilization at 50%.

```bash
aws autoscaling put-scaling-policy \
    --auto-scaling-group-name devops-asg \
    --policy-name devops-cpu-target-tracking \
    --policy-type TargetTrackingScaling \
    --target-tracking-configuration '{
        "PredefinedMetricSpecification": {
            "PredefinedMetricType": "ASGAverageCPUUtilization"
        },
        "TargetValue": 50.0
    }' \
    --region us-east-1
```

This policy automatically adjusts the number of EC2 instances based on average CPU utilization.

---

# 9. Verify Auto Scaling Group

To check the Auto Scaling Group:

```bash
aws autoscaling describe-auto-scaling-groups \
    --auto-scaling-group-names devops-asg \
    --region us-east-1
```

To display its capacity configuration:

```bash
aws autoscaling describe-auto-scaling-groups \
    --auto-scaling-group-names devops-asg \
    --query 'AutoScalingGroups[0].[MinSize,DesiredCapacity,MaxSize]' \
    --output table \
    --region us-east-1
```

Expected configuration:

```text
Minimum:  1
Desired:  1
Maximum:  2
```

---

# 10. Create the Target Group

The target group configuration:

```text
Name: devops-tg
Target Type: Instance
Protocol: HTTP
Port: 80
Health Check Path: /
```

The CLI command:

```bash
aws elbv2 create-target-group \
    --name devops-tg \
    --protocol HTTP \
    --port 80 \
    --target-type instance \
    --vpc-id vpc-xxxxxxxxxxxxxxxxx \
    --health-check-protocol HTTP \
    --health-check-path / \
    --region us-east-1
```

Replace the example VPC ID with the actual VPC ID.

---

# 11. Verify Target Group

To verify the target group:

```bash
aws elbv2 describe-target-groups \
    --names devops-tg \
    --region us-east-1
```

---

# 12. Create the Application Load Balancer

The required ALB configuration:

```text
Name: devops-alb
Type: Application Load Balancer
Scheme: Internet-facing
IP Address Type: IPv4
```

The CLI command:

```bash
aws elbv2 create-load-balancer \
    --name devops-alb \
    --type application \
    --scheme internet-facing \
    --ip-address-type ipv4 \
    --subnets subnet-xxxxxxxx subnet-yyyyyyyy \
    --security-groups sg-xxxxxxxxxxxxxxxxx \
    --region us-east-1
```

Replace the example subnet and security group IDs with the actual IDs.

---

# 13. Create HTTP Listener

The ALB listener was configured to accept HTTP requests on port 80 and forward them to the target group.

```bash
aws elbv2 create-listener \
    --load-balancer-arn <ALB-ARN> \
    --protocol HTTP \
    --port 80 \
    --default-actions Type=forward,TargetGroupArn=<TARGET-GROUP-ARN> \
    --region us-east-1
```

Replace the placeholders with the actual ALB and target group ARNs.

---

# 14. Attach Target Group to Auto Scaling Group

To associate the target group with the Auto Scaling Group:

```bash
aws autoscaling attach-load-balancer-target-groups \
    --auto-scaling-group-name devops-asg \
    --target-group-arns <TARGET-GROUP-ARN> \
    --region us-east-1
```

This allows Auto Scaling to automatically register newly launched instances with the target group.

---

# 15. Configure ELB Health Checks

The Auto Scaling Group can use Elastic Load Balancing health checks:

```bash
aws autoscaling update-auto-scaling-group \
    --auto-scaling-group-name devops-asg \
    --health-check-type ELB \
    --health-check-grace-period 300 \
    --region us-east-1
```

This enables the Auto Scaling Group to consider load balancer health information when evaluating instance health.

---

# 16. Verify Registered Targets

To check registered EC2 instances:

```bash
aws elbv2 describe-target-health \
    --target-group-arn <TARGET-GROUP-ARN> \
    --region us-east-1
```

Expected healthy target status:

```text
State: healthy
```

---

# 17. Verify Load Balancer

To check the Application Load Balancer:

```bash
aws elbv2 describe-load-balancers \
    --names devops-alb \
    --region us-east-1
```

To retrieve its DNS name:

```bash
aws elbv2 describe-load-balancers \
    --names devops-alb \
    --query 'LoadBalancers[0].DNSName' \
    --output text \
    --region us-east-1
```

---

# 18. Test the Application Using ALB DNS

After retrieving the DNS name, I tested the application:

```bash
curl http://<ALB-DNS-NAME>
```

To check only the HTTP response headers:

```bash
curl -I http://<ALB-DNS-NAME>
```

Expected HTTP response:

```text
HTTP/1.1 200 OK
```

The response body should contain the default Nginx welcome page HTML.

---

# 19. Verify Auto Scaling Instances

To list the EC2 instances associated with the Auto Scaling Group:

```bash
aws autoscaling describe-auto-scaling-instances \
    --region us-east-1 \
    --query 'AutoScalingInstances[*].[InstanceId,AutoScalingGroupName,LifecycleState]' \
    --output table
```

---

# 20. Verify Scaling Policy

To check the scaling policy:

```bash
aws autoscaling describe-policies \
    --auto-scaling-group-name devops-asg \
    --region us-east-1
```

This verifies the configured target tracking policy.

---

# Important Notes

- Always use the correct AWS region: `us-east-1`.
- Replace example AMI, VPC, subnet, security group, and ARN values with actual resource IDs.
- The EC2 instance security group must allow HTTP traffic from the ALB security group.
- The ALB security group must allow inbound HTTP traffic on port 80.
- An internet-facing ALB requires appropriate public subnets and internet connectivity.
- The target group must be associated with the Auto Scaling Group.
- The target tracking policy uses average CPU utilization, not individual instance CPU utilization.
- User Data runs during the initial instance boot process.
- Allow sufficient time for EC2 initialization and target health checks before testing the ALB.

---

# Day 44 Result

```text
Launch Template : devops-launch-template
Auto Scaling Group : devops-asg
Minimum Capacity : 1
Desired Capacity : 1
Maximum Capacity : 2
CPU Target : 50%
Target Group : devops-tg
Load Balancer : devops-alb
Listener : HTTP : 80
Region : us-east-1
Application : Nginx
Status : Successfully Configured
```

**Day 44 — Completed Successfully ✅**

100 Days. 100 Challenges. One Cloud Journey. ☁️🚀
