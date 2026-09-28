# Day 10 - Elastic IP Verification Commands

This file contains only the **AWS CLI verification commands** used to verify that the public Elastic IP was successfully assigned to the required EC2 instance.

The resource itself was created and associated using the **AWS Management Console**.

---

# 1. Verify AWS Identity

First, I verified that the AWS CLI was connected to the KodeKloud lab account:

```bash
aws sts get-caller-identity
```

---

# 2. Verify EC2 Instances

I listed the EC2 instances in the required region:

```bash
aws ec2 describe-instances \
    --region us-east-1 \
    --query 'Reservations[].Instances[].[InstanceId,State.Name,PrivateIpAddress,PublicIpAddress]' \
    --output table
```

This verifies:

```text
Instance ID
Instance State
Private IP Address
Public IP Address
```

---

# 3. Verify the Instance Public IP

To check the public IPv4 address assigned to a specific instance:

```bash
aws ec2 describe-instances \
    --region us-east-1 \
    --instance-ids <instance-id> \
    --query 'Reservations[0].Instances[0].[InstanceId,PrivateIpAddress,PublicIpAddress]' \
    --output table
```

The output should show the Elastic IP as the instance's public IPv4 address.

---

# 4. Verify Elastic IP Addresses

I listed the Elastic IP addresses in the `us-east-1` region:

```bash
aws ec2 describe-addresses \
    --region us-east-1
```

This displays information about the allocated Elastic IP addresses.

---

# 5. Display Elastic IP and Instance Association

To display the Elastic IP together with its associated EC2 instance:

```bash
aws ec2 describe-addresses \
    --region us-east-1 \
    --query 'Addresses[].[PublicIp,AllocationId,AssociationId,InstanceId,PrivateIpAddress]' \
    --output table
```

The output verifies:

```text
Public IP
Allocation ID
Association ID
Instance ID
Private IP
```

---

# 6. Verify a Specific Elastic IP

If the Elastic IP address is known:

```bash
aws ec2 describe-addresses \
    --region us-east-1 \
    --public-ips <elastic-ip>
```

This verifies the details of the specific Elastic IP.

---

# 7. Verify Elastic IP Association

To display only the important association information:

```bash
aws ec2 describe-addresses \
    --region us-east-1 \
    --query 'Addresses[].[PublicIp,InstanceId,PrivateIpAddress,AssociationId]' \
    --output table
```

Expected information:

```text
Public IP       Instance ID       Private IP       Association ID
--------------  ----------------  ---------------  ----------------
<elastic-ip>    <instance-id>     <private-ip>     <association-id>
```

---

# 8. Verify Network Interface

The EC2 instance's network interface can also be checked:

```bash
aws ec2 describe-network-interfaces \
    --region us-east-1 \
    --query 'NetworkInterfaces[].[NetworkInterfaceId,PrivateIpAddress,Association.PublicIp,Attachment.InstanceId]' \
    --output table
```

This verifies the relationship between:

```text
Network Interface
       |
       +-- Private IP
       |
       +-- Public / Elastic IP
       |
       +-- EC2 Instance
```

---

# 9. Verify Complete EC2 Networking Information

To display the networking information for a specific instance:

```bash
aws ec2 describe-instances \
    --region us-east-1 \
    --instance-ids <instance-id> \
    --query 'Reservations[0].Instances[0].[InstanceId,PrivateIpAddress,PublicIpAddress,NetworkInterfaces[0].NetworkInterfaceId]' \
    --output table
```

---

# Final Verification

The expected result is:

```text
Elastic IP       : <elastic-ip>
Instance ID      : <instance-id>
Private IP       : <private-ip>
Association ID   : <association-id>
Region           : us-east-1
Status           : Associated
```

The Elastic IP should appear as the **Public IPv4 address** of the associated EC2 instance.

---

# Day 10 Result

```text
EC2 Instance
      |
      +-- Private IP
      |
      +-- Network Interface
      |
      +-- Elastic IP
              |
              v
        Successfully Associated
```

**Day 10 — Verification Completed Successfully**
