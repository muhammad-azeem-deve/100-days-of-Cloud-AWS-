# Day 11 - Elastic Network Interface Verification Commands

This file contains **verification commands only** for Day 11 of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The Elastic Network Interface was attached to the EC2 instance using the **AWS Management Console**.

The commands below were used only to verify the configuration.

---

# 1. Verify AWS Identity

```bash
aws sts get-caller-identity
```

This confirms that the AWS CLI is using the expected lab account.

---

# 2. Verify Network Interface

To view the network interface details:

```bash
aws ec2 describe-network-interfaces \
    --region us-east-1
```

---

# 3. Verify Specific ENI

To verify a specific Elastic Network Interface:

```bash
aws ec2 describe-network-interfaces \
    --network-interface-ids <eni-id> \
    --region us-east-1
```

Replace:

```text
<eni-id>
```

with the actual Network Interface ID.

---

# 4. Verify ENI Attachment

To display the ENI and its attachment information:

```bash
aws ec2 describe-network-interfaces \
    --network-interface-ids <eni-id> \
    --region us-east-1 \
    --query 'NetworkInterfaces[0].[NetworkInterfaceId,Attachment.InstanceId,Attachment.AttachmentId,Attachment.Status]' \
    --output table
```

The output should show information similar to:

```text
---------------------------------------------------------
|                DescribeNetworkInterfaces              |
+----------------+----------------------+---------------+
| eni-xxxxxxxx   | i-xxxxxxxxxxxxxxxx   | eni-attach-xx |
+----------------+----------------------+---------------+
| attached       |
+----------------+
```

---

# 5. Final Verification

The following command provides the main information needed to confirm the attachment:

```bash
aws ec2 describe-network-interfaces \
    --network-interface-ids <eni-id> \
    --region us-east-1 \
    --query 'NetworkInterfaces[0].[NetworkInterfaceId,Attachment.InstanceId,Attachment.Status,PrivateIpAddress,SubnetId]' \
    --output table
```

Expected attachment status:

```text
attached
```

Expected relationship:

```text
ENI
 |
 +---- Attachment.Status: attached
 |
 +---- Attachment.InstanceId: <EC2-instance-id>
 |
 +---- PrivateIpAddress: <private-ip>
 |
 +---- SubnetId: <subnet-id>
```

---

# Day 11 Verification Result

```text
ENI Status       : attached
EC2 Instance     : Successfully Associated
Private IP       : Verified
Subnet            : Verified
AWS Region        : us-east-1
Verification     : Successful
```

**Day 11 — Completed Successfully **
