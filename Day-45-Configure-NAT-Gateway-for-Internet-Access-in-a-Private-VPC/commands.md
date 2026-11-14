# Day 45 - AWS NAT Gateway Commands

This file contains the AWS CLI commands for **Day 45** of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The task was to configure a NAT Gateway to provide outbound internet connectivity to a private EC2 instance.

---

## 1. Configure AWS CLI

Check the AWS CLI version:

```bash
aws --version
```

Retrieve the temporary KodeKloud lab credentials:

```bash
showcreds
```

Configure AWS CLI:

```bash
aws configure
```

Set the required region:

```text
us-east-1
```

Verify AWS identity:

```bash
aws sts get-caller-identity
```

---

## 2. Identify the Existing VPC

List available VPCs:

```bash
aws ec2 describe-vpcs \
    --region us-east-1
```

Get the VPC ID:

```bash
VPC_ID=$(aws ec2 describe-vpcs \
    --filters "Name=tag:Name,Values=devops-priv-vpc" \
    --query 'Vpcs[0].VpcId' \
    --output text \
    --region us-east-1)
```

Verify:

```bash
echo $VPC_ID
```

Expected VPC configuration:

```text
Name: devops-priv-vpc
CIDR: 10.1.0.0/16
```

---

## 3. Create the Public Subnet

Create the public subnet:

```bash
aws ec2 create-subnet \
    --vpc-id $VPC_ID \
    --cidr-block 10.1.2.0/24 \
    --tag-specifications \
    'ResourceType=subnet,Tags=[{Key=Name,Value=devops-pub-subnet}]' \
    --region us-east-1
```

List the subnets:

```bash
aws ec2 describe-subnets \
    --filters "Name=vpc-id,Values=$VPC_ID" \
    --region us-east-1
```

---

## 4. Create the Internet Gateway

Create an Internet Gateway:

```bash
aws ec2 create-internet-gateway \
    --tag-specifications \
    'ResourceType=internet-gateway,Tags=[{Key=Name,Value=devops-igw}]' \
    --region us-east-1
```

Get the Internet Gateway ID:

```bash
IGW_ID=$(aws ec2 describe-internet-gateways \
    --filters "Name=tag:Name,Values=devops-igw" \
    --query 'InternetGateways[0].InternetGatewayId' \
    --output text \
    --region us-east-1)
```

Attach the Internet Gateway to the VPC:

```bash
aws ec2 attach-internet-gateway \
    --internet-gateway-id $IGW_ID \
    --vpc-id $VPC_ID \
    --region us-east-1
```

Verify the attachment:

```bash
aws ec2 describe-internet-gateways \
    --internet-gateway-ids $IGW_ID \
    --region us-east-1
```

---

## 5. Create the Public Route Table

Create the public route table:

```bash
aws ec2 create-route-table \
    --vpc-id $VPC_ID \
    --tag-specifications \
    'ResourceType=route-table,Tags=[{Key=Name,Value=devops-pub-rt}]' \
    --region us-east-1
```

Get its ID:

```bash
PUB_RT_ID=$(aws ec2 describe-route-tables \
    --filters "Name=tag:Name,Values=devops-pub-rt" \
    --query 'RouteTables[0].RouteTableId' \
    --output text \
    --region us-east-1)
```

---

## 6. Add the Internet Gateway Route

Add the default route:

```bash
aws ec2 create-route \
    --route-table-id $PUB_RT_ID \
    --destination-cidr-block 0.0.0.0/0 \
    --gateway-id $IGW_ID \
    --region us-east-1
```

Verify the route table:

```bash
aws ec2 describe-route-tables \
    --route-table-ids $PUB_RT_ID \
    --region us-east-1
```

Expected route:

```text
Destination: 0.0.0.0/0
Target:      Internet Gateway
```

---

## 7. Associate the Public Route Table

Get the public subnet ID:

```bash
PUB_SUBNET_ID=$(aws ec2 describe-subnets \
    --filters "Name=tag:Name,Values=devops-pub-subnet" \
    --query 'Subnets[0].SubnetId' \
    --output text \
    --region us-east-1)
```

Associate the public route table:

```bash
aws ec2 associate-route-table \
    --route-table-id $PUB_RT_ID \
    --subnet-id $PUB_SUBNET_ID \
    --region us-east-1
```

---

## 8. Allocate an Elastic IP

Allocate an Elastic IP address:

```bash
aws ec2 allocate-address \
    --domain vpc \
    --region us-east-1
```

Get the Allocation ID:

```bash
EIP_ALLOC_ID=$(aws ec2 describe-addresses \
    --query 'Addresses[-1].AllocationId' \
    --output text \
    --region us-east-1)
```

Verify the Elastic IP:

```bash
aws ec2 describe-addresses \
    --allocation-ids $EIP_ALLOC_ID \
    --region us-east-1
```

---

## 9. Create the NAT Gateway

Create the NAT Gateway:

```bash
aws ec2 create-nat-gateway \
    --subnet-id $PUB_SUBNET_ID \
    --allocation-id $EIP_ALLOC_ID \
    --connectivity-type public \
    --tag-specifications \
    'ResourceType=natgateway,Tags=[{Key=Name,Value=devops-natgw}]' \
    --region us-east-1
```

Get the NAT Gateway ID:

```bash
NAT_ID=$(aws ec2 describe-nat-gateways \
    --filter "Name=tag:Name,Values=devops-natgw" \
    --query 'NatGateways[0].NatGatewayId' \
    --output text \
    --region us-east-1)
```

Check its status:

```bash
aws ec2 describe-nat-gateways \
    --nat-gateway-ids $NAT_ID \
    --region us-east-1
```

Wait until the NAT Gateway becomes available:

```bash
aws ec2 wait nat-gateway-available \
    --nat-gateway-ids $NAT_ID \
    --region us-east-1
```

---

## 10. Create the Private Route Table

Create a dedicated private route table:

```bash
aws ec2 create-route-table \
    --vpc-id $VPC_ID \
    --tag-specifications \
    'ResourceType=route-table,Tags=[{Key=Name,Value=devops-priv-rt}]' \
    --region us-east-1
```

Get the private route table ID:

```bash
PRIV_RT_ID=$(aws ec2 describe-route-tables \
    --filters "Name=tag:Name,Values=devops-priv-rt" \
    --query 'RouteTables[0].RouteTableId' \
    --output text \
    --region us-east-1)
```

---

## 11. Add the NAT Gateway Route

Add the default route through the NAT Gateway:

```bash
aws ec2 create-route \
    --route-table-id $PRIV_RT_ID \
    --destination-cidr-block 0.0.0.0/0 \
    --nat-gateway-id $NAT_ID \
    --region us-east-1
```

Verify:

```bash
aws ec2 describe-route-tables \
    --route-table-ids $PRIV_RT_ID \
    --region us-east-1
```

Expected route:

```text
Destination: 0.0.0.0/0
Target:      NAT Gateway
```

---

## 12. Associate the Private Route Table

Get the private subnet ID:

```bash
PRIV_SUBNET_ID=$(aws ec2 describe-subnets \
    --filters "Name=tag:Name,Values=devops-priv-subnet" \
    --query 'Subnets[0].SubnetId' \
    --output text \
    --region us-east-1)
```

Associate the private route table:

```bash
aws ec2 associate-route-table \
    --route-table-id $PRIV_RT_ID \
    --subnet-id $PRIV_SUBNET_ID \
    --region us-east-1
```

Verify the association:

```bash
aws ec2 describe-route-tables \
    --route-table-ids $PRIV_RT_ID \
    --region us-east-1
```

---

## 13. Verify Both Route Tables

Check the public route table:

```bash
aws ec2 describe-route-tables \
    --route-table-ids $PUB_RT_ID \
    --query 'RouteTables[0].Routes[*].[DestinationCidrBlock,GatewayId]' \
    --output table \
    --region us-east-1
```

Check the private route table:

```bash
aws ec2 describe-route-tables \
    --route-table-ids $PRIV_RT_ID \
    --query 'RouteTables[0].Routes[*].[DestinationCidrBlock,NatGatewayId]' \
    --output table \
    --region us-east-1
```

---

## 14. Verify the NAT Gateway Status

```bash
aws ec2 describe-nat-gateways \
    --nat-gateway-ids $NAT_ID \
    --query 'NatGateways[0].[NatGatewayId,State,SubnetId]' \
    --output table \
    --region us-east-1
```

Expected state:

```text
available
```

---

## 15. Verify the S3 Bucket

The target S3 bucket was:

```text
devops-nat-55134834
```

List the bucket contents:

```bash
aws s3 ls s3://devops-nat-55134834/
```

Check for the test file:

```bash
aws s3 ls s3://devops-nat-55134834/devops-test.txt
```

Expected file:

```text
devops-test.txt
```

The presence of this file confirms that the scheduled upload from the private EC2 instance succeeded.

---

# Important Networking Concepts

### Internet Gateway (IGW)

Provides internet connectivity for resources in a VPC when public routing and public IP addressing are configured.

### NAT Gateway

Allows instances in private subnets to initiate outbound connections to the internet without allowing unsolicited inbound internet connections.

### Elastic IP

A static public IPv4 address allocated to an AWS account and associated with the public NAT Gateway.

### Public Route Table

Routes internet-bound traffic through the Internet Gateway.

```text
0.0.0.0/0 -> IGW
```

### Private Route Table

Routes internet-bound traffic through the NAT Gateway.

```text
0.0.0.0/0 -> NAT Gateway
```

---

# Final Verification

```text
VPC               : devops-priv-vpc
Public Subnet     : devops-pub-subnet
Private Subnet    : devops-priv-subnet
Internet Gateway  : Attached
Public Route Table: devops-pub-rt
Private Route Table: devops-priv-rt
NAT Gateway       : devops-natgw
NAT Gateway State : Available
S3 Bucket         : devops-nat-55134834
Test File         : devops-test.txt
Status            : Successfully Verified
```

---

# Day 45 Result

The NAT Gateway was configured successfully, and the private EC2 instance gained outbound internet connectivity.

The test file `devops-test.txt` appeared in the S3 bucket `devops-nat-55134834`.

**Day 45 — Completed Successfully ✅**

100 Days. 100 Challenges. One Cloud Journey. ☁️🚀
