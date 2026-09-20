# Day 02 - AWS Security Group Commands

This file contains the AWS CLI commands used for **Day 02** of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The task was to create a security group named `xfusion-sg` under the **default VPC** in the `us-east-1` region and configure HTTP and SSH inbound rules.

---

# 1. Check AWS CLI

First, I checked whether the AWS CLI was installed:

```bash
aws --version
```

---

# 2. Retrieve KodeKloud Lab Credentials

The AWS client machine provides the `showcreds` command for retrieving the temporary lab credentials:

```bash
showcreds
```

These credentials are temporary and should not be stored in the GitHub repository.

> **Security Note:** Never commit AWS access keys, secret keys, session tokens, passwords, or other credentials to GitHub.

---

# 3. Configure AWS CLI

The AWS CLI can be configured using:

```bash
aws configure
```

The required region for this challenge was:

```text
us-east-1
```

Example:

```text
AWS Access Key ID: <temporary-access-key>
AWS Secret Access Key: <temporary-secret-key>
Default region name: us-east-1
Default output format: json
```

---

# 4. Verify AWS Identity

I verified the AWS identity using:

```bash
aws sts get-caller-identity
```

This confirms that the AWS CLI is authenticated with the lab account.

---

# 5. Find the Default VPC

The task required the security group to be created under the **default VPC**.

I retrieved the default VPC using:

```bash
aws ec2 describe-vpcs \
    --region us-east-1 \
    --filters Name=isDefault,Values=true
```

To retrieve only the VPC ID:

```bash
aws ec2 describe-vpcs \
    --region us-east-1 \
    --filters Name=isDefault,Values=true \
    --query 'Vpcs[0].VpcId' \
    --output text
```

Example output:

```text
vpc-xxxxxxxxxxxxxxxxx
```

---

# 6. Create the Security Group

I created the security group with the required name and description:

```bash
aws ec2 create-security-group \
    --group-name xfusion-sg \
    --description "Security group for Nautilus App Servers" \
    --vpc-id <default-vpc-id> \
    --region us-east-1
```

The `<default-vpc-id>` should be replaced with the VPC ID obtained from the previous command.

Example:

```bash
aws ec2 create-security-group \
    --group-name xfusion-sg \
    --description "Security group for Nautilus App Servers" \
    --vpc-id vpc-xxxxxxxxxxxxxxxxx \
    --region us-east-1
```

---

# 7. Retrieve the Security Group ID

After creating the security group, I retrieved its Security Group ID:

```bash
aws ec2 describe-security-groups \
    --group-names xfusion-sg \
    --region us-east-1 \
    --query 'SecurityGroups[0].GroupId' \
    --output text
```

Example:

```text
sg-xxxxxxxxxxxxxxxxx
```

---

# 8. Add HTTP Inbound Rule

The challenge required HTTP access on port `80` from `0.0.0.0/0`.

I added the HTTP rule using:

```bash
aws ec2 authorize-security-group-ingress \
    --group-name xfusion-sg \
    --protocol tcp \
    --port 80 \
    --cidr 0.0.0.0/0 \
    --region us-east-1
```

The configuration was:

```text
Type       : HTTP
Protocol   : TCP
Port       : 80
Source     : 0.0.0.0/0
```

---

# 9. Add SSH Inbound Rule

The challenge also required SSH access on port `22` from `0.0.0.0/0`.

I added the SSH rule using:

```bash
aws ec2 authorize-security-group-ingress \
    --group-name xfusion-sg \
    --protocol tcp \
    --port 22 \
    --cidr 0.0.0.0/0 \
    --region us-east-1
```

The configuration was:

```text
Type       : SSH
Protocol   : TCP
Port       : 22
Source     : 0.0.0.0/0
```

---

# 10. Verify the Security Group

I verified the security group using:

```bash
aws ec2 describe-security-groups \
    --group-names xfusion-sg \
    --region us-east-1
```

---

# 11. Verify the Security Group Configuration

To display the security group name, description, VPC ID, and group ID:

```bash
aws ec2 describe-security-groups \
    --group-names xfusion-sg \
    --region us-east-1 \
    --query 'SecurityGroups[0].[GroupId,GroupName,Description,VpcId]' \
    --output table
```

---

# 12. Verify Inbound Rules

To display the inbound rules:

```bash
aws ec2 describe-security-groups \
    --group-names xfusion-sg \
    --region us-east-1 \
    --query 'SecurityGroups[0].IpPermissions' \
    --output json
```

The output should contain rules for:

```text
Port 80 -> 0.0.0.0/0
Port 22 -> 0.0.0.0/0
```

---

# 13. Display Rules in a Simple Format

The configured ports can also be inspected with:

```bash
aws ec2 describe-security-groups \
    --group-names xfusion-sg \
    --region us-east-1 \
    --query 'SecurityGroups[0].IpPermissions[*].[IpProtocol,FromPort,ToPort,IpRanges[*].CidrIp]' \
    --output table
```

Expected configuration:

```text
TCP    80    80    0.0.0.0/0
TCP    22    22    0.0.0.0/0
```

---

# 14. Verify the Region

All commands were executed against the required AWS region:

```text
us-east-1
```

The region was explicitly specified using:

```bash
--region us-east-1
```

This ensured that the security group was created and verified in the correct region.

---

# Important Security Notes

The challenge intentionally required:

```text
HTTP -> 0.0.0.0/0
SSH  -> 0.0.0.0/0
```

These rules allow the respective traffic from any IPv4 address.

For real-world production infrastructure, SSH access is commonly restricted to trusted IP ranges rather than being open to the entire internet.

For this KodeKloud lab, however, the required configuration was:

```text
HTTP : 80  -> 0.0.0.0/0
SSH  : 22  -> 0.0.0.0/0
```

---

# Final Verification

The final configuration was:

```text
Security Group : xfusion-sg
Description    : Security group for Nautilus App Servers
VPC            : Default VPC
Region         : us-east-1
```

Inbound rules:

```text
HTTP
Port       : 80
Protocol   : TCP
Source     : 0.0.0.0/0

SSH
Port       : 22
Protocol   : TCP
Source     : 0.0.0.0/0
```

---

# Day 02 Result

```text
AWS Security Group
        |
        +-- Name: xfusion-sg
        |
        +-- VPC: Default VPC
        |
        +-- Region: us-east-1
        |
        +-- HTTP: Port 80 -> 0.0.0.0/0
        |
        +-- SSH: Port 22 -> 0.0.0.0/0
        |
        +-- Status: Created Successfully
```

**Day 02 — Completed Successfully **

100 Days. 100 Challenges. One Cloud Journey. ☁️🚀
