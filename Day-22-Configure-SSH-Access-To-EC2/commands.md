# Day 22 - AWS EC2 SSH Access Verification Commands

This file contains only the AWS CLI commands used to verify the EC2 instance configuration for **Day 22** of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The task was to launch a `t2.micro` EC2 instance and configure SSH public-key authentication using user data.

> Replace `<INSTANCE_ID>` and `<SECURITY_GROUP_ID>` with the actual values from the AWS Console. Run commands in the lab's configured AWS environment and region.

---

## 1. Verify AWS CLI Availability

```bash
aws --version
```

---

## 2. Verify the Active AWS Identity

```bash
aws sts get-caller-identity
```

This confirms which AWS account and identity the CLI is using.

---

## 3. Verify the EC2 Instance

List instances in the required region:

```bash
aws ec2 describe-instances \
  --region us-east-1 \
  --query 'Reservations[].Instances[].[InstanceId,InstanceType,State.Name,PublicIpAddress]' \
  --output table
```

Check the required instance ID:

```bash
aws ec2 describe-instances \
  --instance-ids <INSTANCE_ID> \
  --region us-east-1 \
  --query 'Reservations[0].Instances[0].[InstanceId,InstanceType,State.Name,PublicIpAddress]' \
  --output table
```

Expected configuration:

```text
Instance Type : t2.micro
Instance State: running
```

The public IP address may be empty if the instance was launched without one.

---


## 4. Verify the SSH Security Group Rule

First, identify the security group ID from the instance details. Then inspect its inbound rules:

```bash
aws ec2 describe-security-groups \
  --group-ids <SECURITY_GROUP_ID> \
  --region us-east-1 \
  --query 'SecurityGroups[0].IpPermissions' \
  --output json
```

Confirm that an inbound rule permits TCP port `22` from the authorized SSH client IP address.

For a safer configuration, avoid allowing SSH from `0.0.0.0/0`.

---

## 5. Verify the Configured User Data

```bash
aws ec2 describe-instance-attribute \
  --instance-id <INSTANCE_ID> \
  --attribute userData \
  --region us-east-1 \
  --query 'UserData.Value' \
  --output text
```

If user data is present, the command returns its Base64-encoded content.

To decode and inspect it on a Linux client:

```bash
aws ec2 describe-instance-attribute \
  --instance-id <INSTANCE_ID> \
  --attribute userData \
  --region us-east-1 \
  --query 'UserData.Value' \
  --output text | base64 --decode
```

**Security note:** Review the decoded script locally before sharing its output. Never put private keys, passwords, or other secrets in user data.

---

## 6. Verify the Generated Public Key Locally

Display the public key:

```bash
cat ~/.ssh/nautilus-ec2-key.pub
```

Inspect the public key fingerprint:

```bash
ssh-keygen -lf ~/.ssh/nautilus-ec2-key.pub
```

Check the private key file's permissions:

```bash
ls -l ~/.ssh/nautilus-ec2-key
```

On Linux, the private key should be readable only by its owner.

---

## 7. Verify SSH Connectivity

After confirming the instance's public IP, network rules, and user name:

```bash
ssh -i ~/.ssh/nautilus-ec2-key \
  -o IdentitiesOnly=yes \
  ec2-user@<EC2_PUBLIC_IP>
```

This is a connectivity test rather than an AWS resource verification command. A successful login confirms that SSH authentication works with the selected user and key.

For Ubuntu AMIs, replace `ec2-user` with `ubuntu`.

---

## 8. Final Verification Checklist

- [ ] AWS CLI identity verified.
- [ ] EC2 instance found in the required region.
- [ ] Instance type verified as `t2.micro`.
- [ ] Instance state verified as `running`.
- [ ] Instance status checks passed.
- [ ] Public IP and security group inspected.
- [ ] SSH inbound rule checked.
- [ ] User data inspected.
- [ ] Public/private SSH key pair verified locally.
- [ ] SSH connectivity tested.

---

## Day 22 Result

```text
Challenge : Configure Secure SSH Access
Service   : Amazon EC2
Type      : t2.micro
Access    : SSH public-key authentication
Method    : EC2 User Data
Verification: AWS CLI and SSH connectivity
```

**Day 22 — Completed Successfully**
