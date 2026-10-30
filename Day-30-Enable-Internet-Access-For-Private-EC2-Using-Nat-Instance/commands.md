# Day 30 - NAT Instance Verification Commands

This file contains **only AWS CLI verification commands** for Day 30 of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The task was to enable internet access for a private EC2 instance using a NAT instance instead of a NAT Gateway.

The AWS resources were created and configured through the **AWS Management Console**. The following commands are used only to verify the configuration.

---

# 1. Verify AWS Identity

Check the AWS account currently connected to the CLI:

```bash
aws sts get-caller-identity
```

---

# 2. Verify the VPC

Check the existing VPC:

```bash
aws ec2 describe-vpcs \
    --filters "Name=tag:Name,Values=datacenter-priv-vpc" \
    --region us-east-1
```

Display its VPC ID and CIDR:

```bash
aws ec2 describe-vpcs \
    --filters "Name=tag:Name,Values=datacenter-priv-vpc" \
    --query 'Vpcs[*].[VpcId,CidrBlock,State]' \
    --output table \
    --region us-east-1
```

---

# 3. Verify the Public Subnet

Check the public subnet:

```bash
aws ec2 describe-subnets \
    --filters "Name=tag:Name,Values=datacenter-pub-subnet" \
    --region us-east-1
```

Display subnet name, CIDR, and availability zone:

```bash
aws ec2 describe-subnets \
    --filters "Name=tag:Name,Values=datacenter-pub-subnet" \
    --query 'Subnets[*].[SubnetId,CidrBlock,AvailabilityZone,MapPublicIpOnLaunch]' \
    --output table \
    --region us-east-1
```

Expected CIDR:

```text
10.1.2.0/24
```

---

# 4. Verify the NAT Security Group

Check the NAT security group:

```bash
aws ec2 describe-security-groups \
    --filters "Name=group-name,Values=datacenter-nat-sg" \
    --region us-east-1
```

Display inbound and outbound rules:

```bash
aws ec2 describe-security-groups \
    --filters "Name=group-name,Values=datacenter-nat-sg" \
    --query 'SecurityGroups[*].[GroupId,GroupName,IpPermissions,IpPermissionsEgress]' \
    --output json \
    --region us-east-1
```

---

# 5. Verify the NAT Instance

Find the NAT instance:

```bash
aws ec2 describe-instances \
    --filters "Name=tag:Name,Values=datacenter-nat-instance" \
    --query 'Reservations[*].Instances[*].[InstanceId,InstanceType,State.Name,PublicIpAddress,PrivateIpAddress,SubnetId]' \
    --output table \
    --region us-east-1
```

Check its detailed configuration:

```bash
aws ec2 describe-instances \
    --filters "Name=tag:Name,Values=datacenter-nat-instance" \
    --region us-east-1
```

Expected instance state:

```text
running
```

---

# 6. Verify Source/Destination Check

Check whether source/destination checking is disabled:

```bash
aws ec2 describe-instance-attribute \
    --instance-id <NAT_INSTANCE_ID> \
    --attribute sourceDestCheck \
    --region us-east-1
```

Expected result:

```json
{
    "SourceDestCheck": {
        "Value": false
    }
}
```

The value must be `false` for the NAT instance to forward traffic.

---

# 7. Verify the Private EC2 Instance

Find the private EC2 instance:

```bash
aws ec2 describe-instances \
    --filters "Name=tag:Name,Values=datacenter-priv-ec2" \
    --query 'Reservations[*].Instances[*].[InstanceId,State.Name,PrivateIpAddress,SubnetId,VpcId]' \
    --output table \
    --region us-east-1
```

Check its network configuration:

```bash
aws ec2 describe-instances \
    --filters "Name=tag:Name,Values=datacenter-priv-ec2" \
    --region us-east-1
```

---

# 8. Verify the Route Tables

List route tables in the VPC:

```bash
aws ec2 describe-route-tables \
    --filters "Name=vpc-id,Values=<VPC_ID>" \
    --region us-east-1
```

Verify the public route table:

```bash
aws ec2 describe-route-tables \
    --filters "Name=association.subnet-id,Values=<PUBLIC_SUBNET_ID>" \
    --query 'RouteTables[*].[RouteTableId,Routes]' \
    --output json \
    --region us-east-1
```

Verify the private route table:

```bash
aws ec2 describe-route-tables \
    --filters "Name=association.subnet-id,Values=<PRIVATE_SUBNET_ID>" \
    --query 'RouteTables[*].[RouteTableId,Routes]' \
    --output json \
    --region us-east-1
```

The private route table should contain:

```text
Destination: 0.0.0.0/0
Target: NAT Instance ID
```

Check the NAT instance route specifically:

```bash
aws ec2 describe-route-tables \
    --filters "Name=association.subnet-id,Values=<PRIVATE_SUBNET_ID>" \
    --query 'RouteTables[*].Routes[?DestinationCidrBlock==`0.0.0.0/0`]' \
    --output table \
    --region us-east-1
```

---

# 9. Verify Internet Gateway Attachment

List Internet Gateways attached to the VPC:

```bash
aws ec2 describe-internet-gateways \
    --filters "Name=attachment.vpc-id,Values=<VPC_ID>" \
    --query 'InternetGateways[*].[InternetGatewayId,Attachments[*].State]' \
    --output table \
    --region us-east-1
```

---

# 10. Verify the S3 Bucket

Check that the S3 bucket exists:

```bash
aws s3api head-bucket \
    --bucket datacenter-nat-147281969
```

List the contents of the bucket:

```bash
aws s3 ls s3://datacenter-nat-147281969/
```

Check the uploaded file:

```bash
aws s3api head-object \
    --bucket datacenter-nat-147281969 \
    --key datacenter-test.txt
```

Display file metadata:

```bash
aws s3api head-object \
    --bucket datacenter-nat-147281969 \
    --key datacenter-test.txt \
    --query '{File:Key,Size:ContentLength,LastModified:LastModified}'
```

---

# 11. Verify the Latest File Upload Timestamp

Check the last modified timestamp:

```bash
aws s3api head-object \
    --bucket datacenter-nat-147281969 \
    --key datacenter-test.txt \
    --query 'LastModified' \
    --output text
```

Run the command again after some time and compare the timestamps.

If the timestamp keeps updating, it confirms that the cron job is periodically uploading the file.

---

# 12. Verify All EC2 Instances

List the NAT and private EC2 instances:

```bash
aws ec2 describe-instances \
    --filters \
        "Name=tag:Name,Values=datacenter-nat-instance,datacenter-priv-ec2" \
    --query 'Reservations[*].Instances[*].[Tags[?Key==`Name`].Value|[0],InstanceId,State.Name,PrivateIpAddress,PublicIpAddress]' \
    --output table \
    --region us-east-1
```

---

# Important Notes

- Replace `<VPC_ID>` with the actual VPC ID.
- Replace `<PUBLIC_SUBNET_ID>` with the actual public subnet ID.
- Replace `<PRIVATE_SUBNET_ID>` with the subnet ID containing the private EC2 instance.
- Replace `<NAT_INSTANCE_ID>` with the actual NAT instance ID.
- These commands verify AWS resources and configurations; they do not create or modify infrastructure.
- The S3 verification commands must be executed using credentials with the required S3 permissions.
- CLI verification of the S3 object confirms its presence and timestamp, while the AWS Console provides another way to inspect the upload.

---

# Day 30 Result

```text
VPC                    : datacenter-priv-vpc
Public Subnet          : datacenter-pub-subnet
NAT Security Group     : datacenter-nat-sg
NAT Instance           : datacenter-nat-instance
Source/Dest Check      : Disabled
Private EC2            : datacenter-priv-ec2
S3 Bucket              : datacenter-nat-147281969
Uploaded File          : datacenter-test.txt
Cron Job Upload        : Verified
Status                 : Successfully Completed
```

**Day 30 — Completed Successfully ✅**

100 Days. 100 Challenges. One Cloud Journey. ☁️🚀
