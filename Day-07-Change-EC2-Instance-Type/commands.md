# Day 07 - AWS EC2 Instance Type Commands

This file contains the AWS CLI commands used to verify and manage **Day 07** of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The task was to change the EC2 instance named `datacenter-ec2` from:

```text
t2.micro
```

to:

```text
t2.nano
```

and make sure that the instance was in the **running** state after the change.

The required AWS region was:

```text
us-east-1
```

---

# 1. Check AWS CLI

First, I verified that the AWS CLI was available:

```bash
aws --version
```

---

# 2. Retrieve KodeKloud Lab Credentials

The KodeKloud AWS client provides the `showcreds` command to retrieve the temporary credentials:

```bash
showcreds
```

These credentials were used only for the active lab session.

> **Security Note:** Never add AWS credentials to `README.md`, `commands.md`, GitHub, screenshots, or any other public repository.

---

# 3. Verify AWS Identity

I verified the active AWS identity:

```bash
aws sts get-caller-identity
```

This confirms which AWS account and identity are being used by the AWS CLI.

---

# 4. Verify the Required Region

The challenge required the resource to be managed in:

```text
us-east-1
```

For EC2 commands, I explicitly specified the region:

```bash
--region us-east-1
```

---

# 5. Find the datacenter-ec2 Instance

I searched for the EC2 instance using its Name tag:

```bash
aws ec2 describe-instances \
    --region us-east-1 \
    --filters "Name=tag:Name,Values=datacenter-ec2" \
    --query 'Reservations[].Instances[].{InstanceId:InstanceId,Type:InstanceType,State:State.Name}' \
    --output table
```

This allowed me to identify the instance ID, current instance type, and current state.

---

# 6. Verify the Current Instance Type

Before making any changes, I checked the current instance type:

```bash
aws ec2 describe-instances \
    --region us-east-1 \
    --filters "Name=tag:Name,Values=datacenter-ec2" \
    --query 'Reservations[].Instances[].InstanceType' \
    --output text
```

Expected initial output:

```text
t2.micro
```

---

# 7. Check the Instance State

I checked the current state of the instance:

```bash
aws ec2 describe-instances \
    --region us-east-1 \
    --filters "Name=tag:Name,Values=datacenter-ec2" \
    --query 'Reservations[].Instances[].State.Name' \
    --output text
```

The instance was checked before proceeding with the configuration change.

---

# 8. Stop the Instance

Changing an EC2 instance type requires the instance to be stopped.

First, I obtained the instance ID:

```bash
INSTANCE_ID=$(aws ec2 describe-instances \
    --region us-east-1 \
    --filters "Name=tag:Name,Values=datacenter-ec2" \
    --query 'Reservations[].Instances[0].InstanceId' \
    --output text)
```

Then I stopped the instance:

```bash
aws ec2 stop-instances \
    --instance-ids "$INSTANCE_ID" \
    --region us-east-1
```

---

# 9. Wait Until the Instance Is Stopped

I waited until the instance reached the `stopped` state:

```bash
aws ec2 wait instance-stopped \
    --instance-ids "$INSTANCE_ID" \
    --region us-east-1
```

This ensures that the instance has completely stopped before changing its type.

---

# 10. Change the Instance Type

After the instance was stopped, I changed the instance type from `t2.micro` to `t2.nano`:

```bash
aws ec2 modify-instance-attribute \
    --instance-id "$INSTANCE_ID" \
    --instance-type "{\"Value\":\"t2.nano\"}" \
    --region us-east-1
```

The new instance type was:

```text
t2.nano
```

---

# 11. Verify the New Instance Type

I verified the new instance type:

```bash
aws ec2 describe-instances \
    --region us-east-1 \
    --instance-ids "$INSTANCE_ID" \
    --query 'Reservations[0].Instances[0].InstanceType' \
    --output text
```

Expected output:

```text
t2.nano
```

---

# 12. Start the EC2 Instance

After successfully changing the instance type, I started the instance:

```bash
aws ec2 start-instances \
    --instance-ids "$INSTANCE_ID" \
    --region us-east-1
```

---

# 13. Wait Until the Instance Is Running

I waited for the instance to reach the `running` state:

```bash
aws ec2 wait instance-running \
    --instance-ids "$INSTANCE_ID" \
    --region us-east-1
```

---

# 14. Wait for Status Checks

Because the task specifically required the status check to be completed, I waited for the instance status checks to pass:

```bash
aws ec2 wait instance-status-ok \
    --instance-ids "$INSTANCE_ID" \
    --region us-east-1
```

This ensures that the instance has passed the required EC2 status checks.

---

# 15. Verify Final Instance State

I verified the final instance state:

```bash
aws ec2 describe-instances \
    --region us-east-1 \
    --instance-ids "$INSTANCE_ID" \
    --query 'Reservations[0].Instances[0].State.Name' \
    --output text
```

Expected output:

```text
running
```

---

# 16. Verify Final Instance Type

I verified that the instance type was changed successfully:

```bash
aws ec2 describe-instances \
    --region us-east-1 \
    --instance-ids "$INSTANCE_ID" \
    --query 'Reservations[0].Instances[0].InstanceType' \
    --output text
```

Expected output:

```text
t2.nano
```

---

# 17. Final Verification

I used the following command to verify the instance ID, type, and state together:

```bash
aws ec2 describe-instances \
    --region us-east-1 \
    --instance-ids "$INSTANCE_ID" \
    --query 'Reservations[0].Instances[0].{InstanceId:InstanceId,InstanceType:InstanceType,State:State.Name}' \
    --output table
```

Expected result:

```text
----------------------------------------------------------------
|                     DescribeInstances                        |
+----------------------+------------+--------------------------+
| InstanceId           | InstanceType | State                 |
+----------------------+------------+--------------------------+
| i-xxxxxxxxxxxxxxxxx  | t2.nano      | running               |
+----------------------+------------+--------------------------+
```

---

# 18. Verify Status Checks

The instance status can also be checked using:

```bash
aws ec2 describe-instance-status \
    --region us-east-1 \
    --instance-ids "$INSTANCE_ID" \
    --include-all-instances \
    --query 'InstanceStatuses[0].{InstanceState:InstanceState.Name,SystemStatus:SystemStatus.Status,InstanceStatus:InstanceStatus.Status}' \
    --output table
```

The final status should indicate:

```text
InstanceState : running
SystemStatus  : ok
InstanceStatus: ok
```

---

# Complete CLI Workflow

The complete command sequence used for the task was:

```bash
# Verify AWS identity
aws sts get-caller-identity

# Get instance ID
INSTANCE_ID=$(aws ec2 describe-instances \
    --region us-east-1 \
    --filters "Name=tag:Name,Values=datacenter-ec2" \
    --query 'Reservations[].Instances[0].InstanceId' \
    --output text)

# Check current configuration
aws ec2 describe-instances \
    --region us-east-1 \
    --instance-ids "$INSTANCE_ID" \
    --query 'Reservations[0].Instances[0].{InstanceId:InstanceId,Type:InstanceType,State:State.Name}' \
    --output table

# Stop instance
aws ec2 stop-instances \
    --instance-ids "$INSTANCE_ID" \
    --region us-east-1

# Wait until stopped
aws ec2 wait instance-stopped \
    --instance-ids "$INSTANCE_ID" \
    --region us-east-1

# Change instance type
aws ec2 modify-instance-attribute \
    --instance-id "$INSTANCE_ID" \
    --instance-type "{\"Value\":\"t2.nano\"}" \
    --region us-east-1

# Start instance
aws ec2 start-instances \
    --instance-ids "$INSTANCE_ID" \
    --region us-east-1

# Wait until running
aws ec2 wait instance-running \
    --instance-ids "$INSTANCE_ID" \
    --region us-east-1

# Wait for status checks
aws ec2 wait instance-status-ok \
    --instance-ids "$INSTANCE_ID" \
    --region us-east-1

# Final verification
aws ec2 describe-instances \
    --region us-east-1 \
    --instance-ids "$INSTANCE_ID" \
    --query 'Reservations[0].Instances[0].{InstanceId:InstanceId,Type:InstanceType,State:State.Name}' \
    --output table
```

---

# Final Result

```text
Instance Name : datacenter-ec2
Region        : us-east-1

Before:
Instance Type : t2.micro

After:
Instance Type : t2.nano

Final State:
running

Status Checks:
2/2 checks passed
```

**Day 07 — Completed Successfully **

The `datacenter-ec2` instance was changed from **`t2.micro`** to **`t2.nano`**, and the instance was successfully returned to the **running** state after the modification.
