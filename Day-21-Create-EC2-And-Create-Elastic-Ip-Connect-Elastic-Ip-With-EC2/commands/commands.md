# Day 21 - EC2 Instance and Elastic IP Verification Commands

This file contains the AWS CLI verification commands for Day 21 of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The task was to create an EC2 instance, allocate an Elastic IP address, and associate the Elastic IP with the instance using the AWS Management Console.

**AWS Region:** `us-east-1`

---

## 1. Verify AWS CLI Installation

```bash
aws --version
```

---

## 2. Verify AWS Identity

```bash
aws sts get-caller-identity
```

This verifies the AWS account and identity associated with the current CLI credentials.

---

## 3. List EC2 Instances

```bash
aws ec2 describe-instances \
    --region us-east-1 \
    --query 'Reservations[*].Instances[*].[InstanceId,State.Name,InstanceType,PublicIpAddress,PrivateIpAddress]' \
    --output table
```

This command verifies:

- EC2 instance ID.
- Instance state.
- Instance type.
- Public IPv4 address.
- Private IPv4 address.

---

## 4. Verify a Specific EC2 Instance

Replace `<instance-id>` with the actual instance ID from the AWS Console.

```bash
aws ec2 describe-instances \
    --instance-ids <instance-id> \
    --region us-east-1 \
    --query 'Reservations[*].Instances[*].[InstanceId,State.Name,PublicIpAddress,PrivateIpAddress]' \
    --output table
```

Expected state:

```text
running
```

---

## 5. List Allocated Elastic IP Addresses

```bash
aws ec2 describe-addresses \
    --region us-east-1 \
    --query 'Addresses[*].[PublicIp,AllocationId,AssociationId,InstanceId,PrivateIpAddress]' \
    --output table
```

This verifies the allocated Elastic IP addresses and their association details.

---

## 6. Verify the Elastic IP Association

Replace `<elastic-ip>` with the actual Elastic IP address displayed in the AWS Console.

```bash
aws ec2 describe-addresses \
    --public-ips <elastic-ip> \
    --region us-east-1 \
    --query 'Addresses[*].[PublicIp,AllocationId,AssociationId,InstanceId,PrivateIpAddress]' \
    --output table
```

Verify that the output contains:

- The allocated Elastic IP address.
- An allocation ID.
- An association ID.
- The associated EC2 instance ID.
- The corresponding private IPv4 address.

---

## 7. Verify the Instance and Elastic IP Together

Replace `<instance-id>` with the EC2 instance ID.

```bash
aws ec2 describe-addresses \
    --region us-east-1 \
    --query "Addresses[?InstanceId=='<instance-id>'].[PublicIp,AllocationId,AssociationId,InstanceId,PrivateIpAddress]" \
    --output table
```

This command checks whether an Elastic IP address is associated with the specified instance.

---

## 8. Verify the Instance's Public and Private IP Addresses

```bash
aws ec2 describe-instances \
    --region us-east-1 \
    --query 'Reservations[*].Instances[*].[InstanceId,PublicIpAddress,PrivateIpAddress]' \
    --output table
```

The public IPv4 address should match the Elastic IP associated with the instance.

---

## 9. Verify Elastic IP Allocation and Association Status

```bash
aws ec2 describe-addresses \
    --region us-east-1 \
    --query 'Addresses[*].[PublicIp,AllocationId,AssociationId,InstanceId]' \
    --output table
```

A non-empty `AssociationId` and the expected `InstanceId` indicate that the Elastic IP is associated with an instance.

---

## Final Verification Checklist

- [ ] AWS CLI is available.
- [ ] AWS identity is verified.
- [ ] EC2 instance exists in `us-east-1`.
- [ ] EC2 instance is running.
- [ ] Elastic IP address is allocated.
- [ ] Elastic IP is associated with the intended instance.
- [ ] Public IPv4 address matches the Elastic IP.

---

## Day 21 Result

```text
EC2 Instance: Created
Instance State: Running
Elastic IP: Allocated
Elastic IP Association: Verified
AWS Region: us-east-1
Status: Completed Successfully
```

**Day 21 — Completed Successfully**
