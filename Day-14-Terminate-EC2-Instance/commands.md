# Day 14 - EC2 Termination Verification Commands

This file contains **only the AWS CLI verification commands** used for Day 14 of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The EC2 instance was terminated using the **AWS Management Console**.

The commands below were used only to verify the final instance state.

---

# 1. Verify AWS Identity

```bash
aws sts get-caller-identity
```

---

# 2. Verify the EC2 Instance State

Replace `<instance-id>` with the EC2 instance ID:

```bash
aws ec2 describe-instances \
    --instance-ids <instance-id> \
    --region us-east-1 \
    --query 'Reservations[].Instances[].State.Name' \
    --output text
```

Expected output:

```text
terminated
```

---

# 3. Verify Instance ID and State

```bash
aws ec2 describe-instances \
    --instance-ids <instance-id> \
    --region us-east-1 \
    --query 'Reservations[].Instances[].[InstanceId,State.Name]' \
    --output table
```

Expected result:

```text
-----------------------------------------
|          DescribeInstances            |
+----------------------+----------------+
|  <instance-id>       |  terminated    |
+----------------------+----------------+
```

---


# Final Verification

Expected EC2 instance state:

```text
Instance State: terminated
Region: us-east-1
```

**Day 14 — Verification Completed Successfully**
