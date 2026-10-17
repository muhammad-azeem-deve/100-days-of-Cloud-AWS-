# Day 24 - AWS Application Load Balancer Verification Commands

This file contains AWS CLI commands used to verify **Day 24** of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The task was to configure an Application Load Balancer for an EC2 instance and troubleshoot connectivity problems involving security groups, target group health, and Availability Zones.

All commands use the required AWS region:

```text
us-east-1
```


---

## 1. Verify AWS CLI Installation

```bash
aws --version
```

## 2. Verify AWS Identity

```bash
aws sts get-caller-identity
```

## 3. Verify the AWS Region Configuration

```bash
aws configure get region
```

For this lab, the required region was:

```text
us-east-1
```

## 4. List Application Load Balancers

```bash
aws elbv2 describe-load-balancers \
    --region us-east-1 \
    --query 'LoadBalancers[*].[LoadBalancerName,Type,Scheme,State.Code,DNSName,VpcId]' \
    --output table
```

This verifies the load balancer name, type, scheme, state, DNS name, and VPC.

## 5. Verify a Specific Load Balancer

```bash
aws elbv2 describe-load-balancers \
    --names <load-balancer-name> \
    --region us-east-1
```

The load balancer should be in the `active` state after provisioning completes.

## 6. Verify Load Balancer Availability Zones and Subnets

```bash
aws elbv2 describe-load-balancers \
    --names <load-balancer-name> \
    --region us-east-1 \
    --query 'LoadBalancers[0].AvailabilityZones[*].[ZoneName,SubnetId]' \
    --output table
```

This command verifies the Availability Zones and subnet mappings configured for the load balancer.

## 7. List Target Groups

```bash
aws elbv2 describe-target-groups \
    --region us-east-1 \
    --query 'TargetGroups[*].[TargetGroupName,Protocol,Port,VpcId,TargetType,HealthCheckPath]' \
    --output table
```

## 8. Verify a Specific Target Group

```bash
aws elbv2 describe-target-groups \
    --names <target-group-name> \
    --region us-east-1
```

This verifies the target group's protocol, port, VPC, health check settings, and associated load balancers.

## 9. Verify Registered EC2 Targets

```bash
aws elbv2 describe-target-health \
    --target-group-arn <target-group-arn> \
    --region us-east-1
```

This command displays the registered targets, their health states, and any reported health check reasons.

## 10. Display Target Health in a Table

```bash
aws elbv2 describe-target-health \
    --target-group-arn <target-group-arn> \
    --region us-east-1 \
    --query 'TargetHealthDescriptions[*].[Target.Id,Target.Port,TargetHealth.State,TargetHealth.Reason,TargetHealth.Description]' \
    --output table
```

Expected healthy state:

```text
healthy
```

If the target is unhealthy, inspect the reason and description returned by AWS.

## 11. Verify Load Balancer Listeners

```bash
aws elbv2 describe-listeners \
    --load-balancer-arn <load-balancer-arn> \
    --region us-east-1
```

## 12. Verify Listener Ports and Forwarding Rules

```bash
aws elbv2 describe-listeners \
    --load-balancer-arn <load-balancer-arn> \
    --region us-east-1 \
    --query 'Listeners[*].[Port,Protocol,DefaultActions[*].Type,DefaultActions[*].TargetGroupArn]' \
    --output json
```

Verify that the HTTP listener uses port `80` and forwards requests to the intended target group.

## 13. Verify Security Groups

List the security groups in the region's VPCs:

```bash
aws ec2 describe-security-groups \
    --region us-east-1 \
    --query 'SecurityGroups[*].[GroupId,GroupName,VpcId]' \
    --output table
```

Inspect the inbound rules for a specific security group:

```bash
aws ec2 describe-security-groups \
    --group-ids <security-group-id> \
    --region us-east-1 \
    --query 'SecurityGroups[0].IpPermissions' \
    --output json
```

Verify that the load balancer security group permits the required incoming HTTP traffic and that the EC2 security group allows HTTP traffic from the load balancer security group.

## 14. Verify the EC2 Instance State and Network Configuration

```bash
aws ec2 describe-instances \
    --instance-ids <instance-id> \
    --region us-east-1 \
    --query 'Reservations[*].Instances[*].[InstanceId,State.Name,VpcId,SubnetId,Placement.AvailabilityZone,PrivateIpAddress,SecurityGroups[*].GroupId]' \
    --output json
```

Verify that the instance is running and that its VPC, subnet, Availability Zone, and security groups are appropriate for the lab.

## 15. Verify the EC2 Subnet Availability Zone

```bash
aws ec2 describe-subnets \
    --subnet-ids <subnet-id> \
    --region us-east-1 \
    --query 'Subnets[*].[SubnetId,VpcId,AvailabilityZone,State,MapPublicIpOnLaunch]' \
    --output table
```

This helps verify the subnet associated with the EC2 instance and investigate an incorrect Availability Zone selection.

## 16. Verify the Load Balancer DNS Name

Retrieve the DNS name:

```bash
aws elbv2 describe-load-balancers \
    --names <load-balancer-name> \
    --region us-east-1 \
    --query 'LoadBalancers[0].DNSName' \
    --output text
```

Test HTTP connectivity from a machine that has network access to the public load balancer:

```bash
curl -I --connect-timeout 10 http://<load-balancer-dns-name>
```

To display the response body and HTTP status code:

```bash
curl -sS -o /dev/null \
    -w 'HTTP Status: %{http_code}\n' \
    --connect-timeout 10 \
    http://<load-balancer-dns-name>
```

A successful application response may return `200`, or another expected application status. A `503` response often indicates that no suitable healthy targets are available, although other causes are possible.

## 17. Verify the Target Group's Associated Load Balancer

```bash
aws elbv2 describe-target-groups \
    --names <target-group-name> \
    --region us-east-1 \
    --query 'TargetGroups[0].[TargetGroupName,LoadBalancerArns,TargetType,Port]' \
    --output json
```

This verifies that the intended target group is associated with the correct load balancer.

---

# Final Verification Checklist

- [ ] AWS region is `us-east-1`.
- [ ] Application Load Balancer is active.
- [ ] Correct subnets and Availability Zones are configured.
- [ ] Load balancer security group permits the required HTTP traffic.
- [ ] EC2 security group permits HTTP traffic from the load balancer.
- [ ] Target group contains the intended EC2 instance.
- [ ] Target health is `healthy`.
- [ ] HTTP listener uses port `80`.
- [ ] Listener forwards traffic to the correct target group.
- [ ] Load balancer DNS name responds as expected.

---

## Day 24 Result

```text
Service       : Application Load Balancer
Target        : EC2 Instance
Protocol      : HTTP
Port          : 80
Region        : us-east-1
Verification  : AWS CLI
```

**Day 24 — Completed Successfully**
