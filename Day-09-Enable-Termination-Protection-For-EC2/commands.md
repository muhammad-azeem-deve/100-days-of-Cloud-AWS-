# Day 09 - AWS EC2 Termination Protection Commands

This file contains the AWS CLI commands used for **Day 09** of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The task was to enable **Termination Protection** for the EC2 instance `devops-ec2` in the `us-east-1` region and verify the configuration.

---

# 1. Check AWS CLI

First, I checked whether the AWS CLI was available:

```bash
aws --version
```

---

# 2. Verify AWS Identity

I verified the AWS account associated with the temporary KodeKloud credentials:

```bash
aws sts get-caller-identity
```

This command confirms the AWS identity currently being used by the CLI.

---

# 3. Find the EC2 Instance

I searched for the EC2 instance using its Name tag:

```bash
aws ec2 describe-instances \
    --filters "Name=tag:Name,Values=devops-ec2" \
    --region us-east-1 \
    --query 'Reservations[].Instances[].[InstanceId,Tags[?Key==`Name`].Value|[0],State.Name]' \
    --output table
```

The command returns the instance ID, name, and current state.

Example:

```text
-------------------------------------------------
|              DescribeInstances                |
+----------------------+------------+------------+
| Instance ID          | Name       | State      |
+----------------------+------------+------------+
| i-xxxxxxxxxxxxxxxxx  | devops-ec2 | running    |
+----------------------+------------+------------+
```

---

# 4. Save the Instance ID

The instance ID returned from the previous command should be used in the following commands.

Example:

```text
i-xxxxxxxxxxxxxxxxx
```

In the commands below, I use:

```text
<INSTANCE-ID>
```

Replace it with the actual instance ID from the KodeKloud lab.

---

# 5. Enable Termination Protection

Termination protection is enabled using the `modify-instance-attribute` command.

```bash
aws ec2 modify-instance-attribute \
    --instance-id <INSTANCE-ID> \
    --disable-api-termination \
    --region us-east-1
```

The `--disable-api-termination` option enables termination protection. AWS documents this as setting the `DisableApiTermination` attribute to `true`.

The command normally produces no output when it succeeds.

---

# 6. Verify Termination Protection

I verified the configuration using:

```bash
aws ec2 describe-instance-attribute \
    --instance-id <INSTANCE-ID> \
    --attribute disableApiTermination \
    --region us-east-1
```

Expected output:

```json
{
    "InstanceId": "i-xxxxxxxxxxxxxxxxx",
    "DisableApiTermination": {
        "Value": true
    }
}
```

The important value is:

```text
"Value": true
```

This confirms that termination protection is enabled.

---

# 7. Quick Verification

For a simpler output, I used:

```bash
aws ec2 describe-instance-attribute \
    --instance-id <INSTANCE-ID> \
    --attribute disableApiTermination \
    --region us-east-1 \
    --query 'DisableApiTermination.Value' \
    --output text
```

Expected result:

```text
True
```

---

# 8. Verify Instance Information

I also verified the instance information:

```bash
aws ec2 describe-instances \
    --instance-ids <INSTANCE-ID> \
    --region us-east-1 \
    --query 'Reservations[0].Instances[0].[InstanceId,State.Name,InstanceType]' \
    --output table
```

This confirmed that I was checking the correct EC2 instance.

---

# 9. Check Termination Protection Again

The final verification command was:

```bash
aws ec2 describe-instance-attribute \
    --instance-id <INSTANCE-ID> \
    --attribute disableApiTermination \
    --region us-east-1 \
    --query 'DisableApiTermination.Value' \
    --output text
```

Expected output:

```text
True
```

Therefore:

```text
Termination Protection = ENABLED
DisableApiTermination  = true
```

---

# 10. Disable Termination Protection

If termination protection needs to be disabled later, AWS provides the opposite CLI option:

```bash
aws ec2 modify-instance-attribute \
    --instance-id <INSTANCE-ID> \
    --no-disable-api-termination \
    --region us-east-1
```

After disabling it, verification can be performed again:

```bash
aws ec2 describe-instance-attribute \
    --instance-id <INSTANCE-ID> \
    --attribute disableApiTermination \
    --region us-east-1 \
    --query 'DisableApiTermination.Value' \
    --output text
```

Expected result:

```text
False
```

This command was documented for reference only; the objective of Day 09 was to **enable** termination protection.

---

# Important AWS CLI Notes

The AWS CLI uses the following EC2 attribute for termination protection:

```text
disableApiTermination
```

When the value is:

```text
true
```

the instance is protected against termination through the EC2 Console, CLI, or API.

When the value is:

```text
false
```

termination protection is disabled.

---

# Complete Command Workflow

The complete CLI workflow was:

```bash
# Check AWS CLI
aws --version

# Verify AWS identity
aws sts get-caller-identity

# Find the instance
aws ec2 describe-instances \
    --filters "Name=tag:Name,Values=devops-ec2" \
    --region us-east-1 \
    --query 'Reservations[].Instances[].[InstanceId,Tags[?Key==`Name`].Value|[0],State.Name]' \
    --output table

# Enable termination protection
aws ec2 modify-instance-attribute \
    --instance-id <INSTANCE-ID> \
    --disable-api-termination \
    --region us-east-1

# Verify termination protection
aws ec2 describe-instance-attribute \
    --instance-id <INSTANCE-ID> \
    --attribute disableApiTermination \
    --region us-east-1 \
    --query 'DisableApiTermination.Value' \
    --output text
```

Expected final output:

```text
True
```

---

# Final Result

```text
EC2 Instance       : devops-ec2
Region             : us-east-1
Protection         : Termination Protection
AWS Attribute      : DisableApiTermination
Attribute Value    : true
Status             : Successfully Enabled
```

**Day 09 — Completed Successfully **

100 Days. 100 Challenges. One Cloud Journey. ☁️🚀
