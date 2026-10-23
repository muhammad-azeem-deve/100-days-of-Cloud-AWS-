# Day 29 - AWS VPC Peering Verification Commands

This file contains only the AWS CLI commands used to verify **Day 29** of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The objective was to verify the VPC peering connection, VPC CIDR blocks, route tables, EC2 instances, and network connectivity.

---

## 1. Verify AWS Identity

```bash
aws sts get-caller-identity
```

---

## 2. Verify the VPCs and CIDR Blocks

```bash
aws ec2 describe-vpcs \
    --region us-east-1 \
    --filters \
    "Name=cidr-block,Values=172.31.0.0/16,10.1.0.0/16" \
    --query 'Vpcs[*].[VpcId,CidrBlock,State]' \
    --output table
```

---

## 3. Verify the Private VPC

```bash
aws ec2 describe-vpcs \
    --region us-east-1 \
    --filters "Name=tag:Name,Values=xfusion-private-vpc" \
    --query 'Vpcs[*].[VpcId,CidrBlock,State,Tags]' \
    --output table
```

---

## 4. Verify the VPC Peering Connection

```bash
aws ec2 describe-vpc-peering-connections \
    --region us-east-1 \
    --filters "Name=tag:Name,Values=xfusion-vpc-peering" \
    --query 'VpcPeeringConnections[*].[VpcPeeringConnectionId,Status.Code,RequesterVpcInfo.VpcId,RequesterVpcInfo.CidrBlock,AccepterVpcInfo.VpcId,AccepterVpcInfo.CidrBlock]' \
    --output table
```

Expected status:

```text
active
```

---

## 5. Verify the Public EC2 Instance

```bash
aws ec2 describe-instances \
    --region us-east-1 \
    --filters "Name=tag:Name,Values=xfusion-public-ec2" \
    --query 'Reservations[*].Instances[*].[InstanceId,State.Name,PrivateIpAddress,VpcId,SubnetId]' \
    --output table
```

---

## 6. Verify the Private EC2 Instance

```bash
aws ec2 describe-instances \
    --region us-east-1 \
    --filters "Name=tag:Name,Values=xfusion-private-ec2" \
    --query 'Reservations[*].Instances[*].[InstanceId,State.Name,PrivateIpAddress,VpcId,SubnetId]' \
    --output table
```

Expected private IP address:

```text
10.1.1.62
```

---

## 7. Verify Route Tables

```bash
aws ec2 describe-route-tables \
    --region us-east-1 \
    --query 'RouteTables[*].[RouteTableId,VpcId,Routes[*].[DestinationCidrBlock,VpcPeeringConnectionId,State]]' \
    --output json
```

---

## 8. Verify the Default VPC Route

```bash
aws ec2 describe-route-tables \
    --region us-east-1 \
    --filters "Name=route.destination-cidr-block,Values=10.1.0.0/16" \
    --query 'RouteTables[*].[RouteTableId,VpcId,Routes[?DestinationCidrBlock==`10.1.0.0/16`].[DestinationCidrBlock,VpcPeeringConnectionId,State]]' \
    --output json
```

Expected destination:

```text
10.1.0.0/16
```

The route target should be the peering connection, and its state should be `active`.

---

## 9. Verify the Private VPC Route

```bash
aws ec2 describe-route-tables \
    --region us-east-1 \
    --filters "Name=route.destination-cidr-block,Values=172.31.0.0/16" \
    --query 'RouteTables[*].[RouteTableId,VpcId,Routes[?DestinationCidrBlock==`172.31.0.0/16`].[DestinationCidrBlock,VpcPeeringConnectionId,State]]' \
    --output json
```

Expected destination:

```text
172.31.0.0/16
```

The route target should be the peering connection, and its state should be `active`.

---

## 10. Verify the Private Instance Security Groups

First, retrieve the private EC2 instance's security group IDs:

```bash
aws ec2 describe-instances \
    --region us-east-1 \
    --filters "Name=tag:Name,Values=xfusion-private-ec2" \
    --query 'Reservations[*].Instances[*].SecurityGroups[*].[GroupId,GroupName]' \
    --output table
```

Then inspect the security group rules:

```bash
aws ec2 describe-security-groups \
    --region us-east-1 \
    --query 'SecurityGroups[*].[GroupId,GroupName,IpPermissions,IpPermissionsEgress]' \
    --output json
```

Verify that the applicable rules permit the intended ICMP and SSH traffic.

---

## 11. Verify Network ACLs

```bash
aws ec2 describe-network-acls \
    --region us-east-1 \
    --query 'NetworkAcls[*].[NetworkAclId,VpcId,Associations,Entries]' \
    --output json
```

Check that the applicable inbound and outbound rules permit the required traffic.

---

## 12. Verify Connectivity from the Public EC2 Instance

Run this command **on `xfusion-public-ec2`**, not on `aws-client`:

```bash
ping -c 4 10.1.1.62
```

Expected successful result:

```text
4 packets transmitted, 4 received, 0% packet loss
```

---

## 13. Optional: Verify SSH Port Reachability

If SSH access is required and `nc` is installed on the public EC2 instance:

```bash
nc -vz -w 5 10.1.1.62 22
```

A successful TCP connection verifies that port 22 is reachable. It does not prove that SSH public-key authentication will succeed.

---

## Day 29 Verification Summary

```text
VPC Peering Connection : xfusion-vpc-peering
Peering Status         : active
Default VPC CIDR       : 172.31.0.0/16
Private VPC CIDR       : 10.1.0.0/16
Private EC2 IP         : 10.1.1.62
Routing                : Verified
ICMP Connectivity      : Successful
Packet Loss            : 0%
```

**Day 29 — Verification Commands Completed ✅**
