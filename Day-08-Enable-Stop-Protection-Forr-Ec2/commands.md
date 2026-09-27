# Day 08 - EC2 Stop Protection Commands

This file contains the AWS CLI commands used to verify the **EC2 Stop Protection** configuration for Day 08 of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The actual configuration was performed using the **AWS Management Console**, while the AWS CLI was used for verification.

---

# 1. Check AWS CLI

First, I verified that the AWS CLI was available:

```bash
aws --version
```

---

# 2. Verify AWS Identity

I verified the AWS account and identity associated with the temporary KodeKloud credentials:

```bash
aws sts get-caller-identity
```

This confirmed that the AWS CLI was connected to the correct lab environment.

---

# 3. Set the Required AWS Region

The KodeKloud lab required resources to be managed in:

```text
us-east-1
```

Therefore, I used:

```bash
--region us-east-1
```

with the EC2 commands.

---

# 4. List EC2 Instances

I listed the EC2 instances available in the required region:

```bash
aws ec2 describe-instances \
    --region us-east-1 \
    --query 'Reservations[*].Instances[*].[InstanceId,State.Name,Tags[?Key==`Name`]|[0].Value]' \
    --output table
```

This helped me identify the instance that needed Stop Protection enabled.

---

# 5. Identify the Instance ID

The output provided the EC2 instance ID.

Example:

```text
i-0123456789abcdef0
```

The actual instance ID depends on the KodeKloud lab environment.

I stored the instance ID for the verification command.

---

# 6. Verify Stop Protection

After enabling Stop Protection through the AWS Console, I used the following command to verify the configuration:

```bash
aws ec2 describe-instance-attribute \
    --instance-id <instance-id> \
    --attribute disableApiStop \
    --region us-east-1
```

Example:

```bash
aws ec2 describe-instance-attribute \
    --instance-id i-0123456789abcdef0 \
    --attribute disableApiStop \
    --region us-east-1
```

Expected output:

```json
{
    "InstanceId": "i-0123456789abcdef0",
    "DisableApiStop": {
        "Value": true
    }
}
```

---

# 7. Display Only the Stop Protection Value

To display only the protection status:

```bash
aws ec2 describe-instance-attribute \
    --instance-id <instance-id> \
    --attribute disableApiStop \
    --region us-east-1 \
    --query 'DisableApiStop.Value'
```

Expected output:

```text
true
```

The value:

```text
true
```

means that Stop Protection is enabled.

---

# 8. Verify Instance State

I also checked the current state of the EC2 instance:

```bash
aws ec2 describe-instances \
    --instance-ids <instance-id> \
    --region us-east-1 \
    --query 'Reservations[0].Instances[0].[InstanceId,State.Name]' \
    --output table
```

Example:

```text
---------------------------------
|       DescribeInstances       |
+----------------------+--------+
| i-0123456789abcdef0 | running|
+----------------------+--------+
```

---

# 9. Optional: Enable Stop Protection Using CLI

Although the challenge was completed using the AWS Console, the same configuration can also be enabled through the AWS CLI.

The command is:

```bash
aws ec2 modify-instance-attribute \
    --instance-id <instance-id> \
    --disable-api-stop \
    --region us-east-1
```

This enables Stop Protection for the specified EC2 instance.

For this challenge, I used the **AWS Console to make the change** and the CLI to verify it.

---

# 10. Optional: Disable Stop Protection

If Stop Protection needs to be disabled later, the following command can be used:

```bash
aws ec2 modify-instance-attribute \
    --instance-id <instance-id> \
    --no-disable-api-stop \
    --region us-east-1
```

This command was **not required for the challenge**.

---

# Console Verification

After enabling the protection through the AWS Console, I checked the EC2 instance settings.

The expected configuration was:

```text
Instance Settings
        |
        v
Stop Protection
        |
        v
Enabled
```

---

# CLI Verification

The final CLI verification returned:

```text
true
```

using:

```bash
aws ec2 describe-instance-attribute \
    --instance-id <instance-id> \
    --attribute disableApiStop \
    --region us-east-1 \
    --query 'DisableApiStop.Value'
```

---

# Important Notes

Stop Protection is represented in the AWS CLI by the:

```text
DisableApiStop
```

instance attribute.

When:

```text
DisableApiStop = true
```

the EC2 instance has Stop Protection enabled.

When:

```text
DisableApiStop = false
```

Stop Protection is not enabled.

---

# Final Result

```text
EC2 Instance
     |
     +-- Region: us-east-1
     |
     +-- Stop Protection: Enabled
     |
     +-- Console Verification: Passed
     |
     +-- CLI Verification: true
```

**Day 08 — Completed Successfully **

AWS Console → Enable Stop Protection → Verify in Console → Verify with AWS CLI

100 Days. 100 Challenges. One Cloud Journey. ☁️🚀
