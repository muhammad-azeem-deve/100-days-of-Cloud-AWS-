# Day 27 - AWS VPC, Subnet, and EC2 Verification Commands

This file contains AWS CLI commands to verify the resources created for **Day 27 of my 100 Days of Cloud (AWS) Challenge by KodeKloud**.

All commands use the required AWS region: `us-east-1`.

---

## 1. Verify AWS Account

```bash
aws sts get-caller-identity \
    --region us-east-1
```

This verifies the AWS identity associated with the active CLI credentials.

---

## 2. Verify the VPC

```bash
aws ec2 describe-vpcs \
    --region us-east-1 \
    --filters "Name=tag:Name,Values=devops-pub-vpc" \
    --query 'Vpcs[*].[VpcId,Tags[?Key==`Name`]|[0].Value,CidrBlock,State]' \
    --output table
```

Expected verification:

- VPC name: `devops-pub-vpc`
- State: `available`

---

## 3. Verify the Subnet

```bash
aws ec2 describe-subnets \
    --region us-east-1 \
    --filters "Name=tag:Name,Values=devops-pub-subnet" \
    --query 'Subnets[*].[SubnetId,VpcId,Tags[?Key==`Name`]|[0].Value,CidrBlock,State,MapPublicIpOnLaunch]' \
    --output table
```

This verifies:

- Subnet name
- Associated VPC ID
- Subnet CIDR block
- Subnet state
- Automatic public IPv4 assignment setting

The `MapPublicIpOnLaunch` field should be `True`.

---

## 4. Verify the Internet Gateway

```bash
aws ec2 describe-internet-gateways \
    --region us-east-1 \
    --filters "Name=attachment.vpc-id,Values=$(aws ec2 describe-vpcs --region us-east-1 --filters 'Name=tag:Name,Values=devops-pub-vpc' --query 'Vpcs[0].VpcId' --output text)" \
    --query 'InternetGateways[*].[InternetGatewayId,Attachments[0].VpcId,Attachments[0].State]' \
    --output table
```

This verifies whether an internet gateway is attached to the VPC.

Expected attachment state:

```text
available
```

If the VPC has no attached internet gateway, this command returns no matching gateway.

---

## 5. Verify the Route Table

```bash
aws ec2 describe-route-tables \
    --region us-east-1 \
    --filters "Name=association.subnet-id,Values=$(aws ec2 describe-subnets --region us-east-1 --filters 'Name=tag:Name,Values=devops-pub-subnet' --query 'Subnets[0].SubnetId' --output text)" \
    --query 'RouteTables[*].[RouteTableId,Routes[*].[DestinationCidrBlock,GatewayId,State]]' \
    --output json
```

This checks the route table explicitly associated with the subnet.

Look for a route with:

```text
Destination: 0.0.0.0/0
Target:      igw-xxxxxxxx
State:       active
```

The route table may be associated implicitly through the VPC's main route table. If the command returns no rows, inspect the main route table as well.

---

## 6. Verify the EC2 Instance

```bash
aws ec2 describe-instances \
    --region us-east-1 \
    --filters "Name=tag:Name,Values=devops-pub-ec2" \
    --query 'Reservations[*].Instances[*].[InstanceId,Tags[?Key==`Name`]|[0].Value,InstanceType,State.Name,VpcId,SubnetId,PublicIpAddress]' \
    --output table
```

Expected verification:

```text
Instance Name : devops-pub-ec2
Instance Type : t2.micro
VPC           : devops-pub-vpc
Subnet        : devops-pub-subnet
State         : running
Public IPv4   : Assigned
```

The public IPv4 address is assigned only if the instance launch and network configuration allow it.

---

## 7. Verify the Security Group

First, retrieve the instance's security group IDs:

```bash
aws ec2 describe-instances \
    --region us-east-1 \
    --filters "Name=tag:Name,Values=devops-pub-ec2" \
    --query 'Reservations[*].Instances[*].SecurityGroups[*].[GroupId,GroupName]' \
    --output table
```

Then inspect the inbound rules for the relevant security group:

```bash
aws ec2 describe-security-groups \
    --region us-east-1 \
    --filters "Name=vpc-id,Values=$(aws ec2 describe-vpcs --region us-east-1 --filters 'Name=tag:Name,Values=devops-pub-vpc' --query 'Vpcs[0].VpcId' --output text)" \
    --query 'SecurityGroups[*].[GroupId,GroupName,IpPermissions]' \
    --output json
```

Verify that the security group attached to the instance allows:

```text
Protocol : TCP
Port     : 22
Source   : The lab-required IP range
```

---

## 8. Verify SSH Port 22 Specifically

This command displays the SSH inbound rule for the security group attached to the instance:

```bash
aws ec2 describe-instances \
    --region us-east-1 \
    --filters "Name=tag:Name,Values=devops-pub-ec2" \
    --query 'Reservations[*].Instances[*].SecurityGroups[*].GroupId' \
    --output text |
tr '\t' '\n' |
while read -r SG_ID; do
    aws ec2 describe-security-groups \
        --region us-east-1 \
        --group-ids "$SG_ID" \
        --query 'SecurityGroups[*].IpPermissions[?FromPort==`22` && ToPort==`22` && IpProtocol==`tcp`]' \
        --output json
done
```

A matching rule confirms that TCP port 22 is allowed by that security group. The source CIDR should also be checked to ensure access is appropriately restricted.

---

## 9. Verify the Subnet and Instance Together

```bash
aws ec2 describe-instances \
    --region us-east-1 \
    --filters "Name=tag:Name,Values=devops-pub-ec2" \
    --query 'Reservations[*].Instances[*].[Tags[?Key==`Name`]|[0].Value,SubnetId,VpcId,InstanceType,State.Name]' \
    --output table
```

This confirms that the EC2 instance is associated with the expected VPC and subnet.

---

## 10. Final Verification Checklist

Run the following commands to verify the main resources:

```bash
# Verify VPC
aws ec2 describe-vpcs \
    --region us-east-1 \
    --filters "Name=tag:Name,Values=devops-pub-vpc"

# Verify subnet and automatic public IP assignment
aws ec2 describe-subnets \
    --region us-east-1 \
    --filters "Name=tag:Name,Values=devops-pub-subnet"

# Verify EC2 instance
aws ec2 describe-instances \
    --region us-east-1 \
    --filters "Name=tag:Name,Values=devops-pub-ec2"

# Verify internet gateways
aws ec2 describe-internet-gateways \
    --region us-east-1

# Verify route tables
aws ec2 describe-route-tables \
    --region us-east-1
```

---

## Day 27 Verification Result

```text
VPC Name        : devops-pub-vpc
Subnet Name     : devops-pub-subnet
Auto Public IP  : Enabled
EC2 Name        : devops-pub-ec2
Instance Type   : t2.micro
SSH Port        : 22
AWS Region      : us-east-1
```

**Day 27 — Verification Commands Completed ✅**

Note: Run these commands in the authorized KodeKloud lab environment. The expected values describe the target configuration; confirm the actual output before marking every verification as successful.
