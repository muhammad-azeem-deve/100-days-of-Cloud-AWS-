# Task 06 - AWS EC2 Commands

This file contains the AWS CLI commands used for **Task 06** of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The task was to create an EC2 instance with the following configuration:

```text
Instance Name : xfusion-ec2
AMI           : Amazon Linux
Instance Type : t2.micro
Key Pair      : xfusion-kp
Key Type      : RSA
Security Group: default
Region        : us-east-1
```

---

# 1. Check AWS CLI

First, I checked whether the AWS CLI was available:

```bash
aws --version
```

---

# 2. Retrieve Lab Credentials

The KodeKloud AWS client provides the `showcreds` command to retrieve temporary lab credentials:

```bash
showcreds
```

These credentials are temporary and should not be stored in the GitHub repository.

---

# 3. Configure AWS CLI

The AWS CLI can be configured using:

```bash
aws configure
```

The required region was:

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

I verified the current AWS identity:

```bash
aws sts get-caller-identity
```

This confirms that the AWS CLI is connected to the lab environment.

---

# 5. Find an Amazon Linux AMI

Because AMI IDs are region-specific and can change between Amazon Linux versions, I queried AWS for an available Amazon Linux 2023 AMI:

```bash
aws ec2 describe-images \
    --owners amazon \
    --region us-east-1 \
    --filters "Name=name,Values=al2023-ami-*" \
    --query 'Images | sort_by(@, &CreationDate)[-1].[ImageId,Name]' \
    --output table
```

The returned AMI ID can then be used when launching the instance.

Example placeholder:

```text
<AMAZON-LINUX-AMI-ID>
```

---

# 6. Find the Default Security Group

The task required the default security group.

I retrieved the default security group information using:

```bash
aws ec2 describe-security-groups \
    --filters Name=group-name,Values=default \
    --region us-east-1 \
    --query 'SecurityGroups[*].[GroupId,GroupName,VpcId]' \
    --output table
```

The command returns the default security group ID.

Example placeholder:

```text
<DEFAULT-SECURITY-GROUP-ID>
```

---

# 7. Create the RSA Key Pair

The required key pair was:

```text
Name : xfusion-kp
Type : RSA
```

I created it using:

```bash
aws ec2 create-key-pair \
    --key-name xfusion-kp \
    --key-type rsa \
    --region us-east-1 \
    --query 'KeyMaterial' \
    --output text > xfusion-kp.pem
```

The private key was saved as:

```text
xfusion-kp.pem
```

---

# 8. Protect the Private Key

The private key should have restricted permissions:

```bash
chmod 400 xfusion-kp.pem
```

This helps prevent unauthorized users from accessing the private key.

---

# 9. Verify the Key Pair

I verified that the key pair was created:

```bash
aws ec2 describe-key-pairs \
    --key-names xfusion-kp \
    --region us-east-1 \
    --query 'KeyPairs[0].[KeyName,KeyType]' \
    --output table
```

Expected result:

```text
--------------------------------
|       DescribeKeyPairs       |
+--------------+---------------+
| xfusion-kp   | rsa           |
+--------------+---------------+
```

---

# 10. Launch the EC2 Instance

After obtaining the Amazon Linux AMI ID and default security group ID, I launched the instance:

```bash
aws ec2 run-instances \
    --image-id <AMAZON-LINUX-AMI-ID> \
    --instance-type t2.micro \
    --key-name xfusion-kp \
    --security-group-ids <DEFAULT-SECURITY-GROUP-ID> \
    --region us-east-1 \
    --tag-specifications 'ResourceType=instance,Tags=[{Key=Name,Value=xfusion-ec2}]'
```

The important configuration was:

```text
Image ID       : Amazon Linux AMI
Instance Type  : t2.micro
Key Pair       : xfusion-kp
Security Group : default
Region         : us-east-1
Name Tag       : xfusion-ec2
```

---

# 11. Find the Created Instance

After launching the instance, I searched for it using the `Name` tag:

```bash
aws ec2 describe-instances \
    --filters "Name=tag:Name,Values=xfusion-ec2" \
    --region us-east-1 \
    --query 'Reservations[*].Instances[*].[InstanceId,InstanceType,State.Name,PrivateIpAddress]' \
    --output table
```

---

# 12. Verify Instance Configuration

I verified the instance name, type, state, and key pair:

```bash
aws ec2 describe-instances \
    --filters "Name=tag:Name,Values=xfusion-ec2" \
    --region us-east-1 \
    --query 'Reservations[*].Instances[*].[InstanceId,InstanceType,State.Name,KeyName]' \
    --output table
```

Expected information:

```text
Instance Name : xfusion-ec2
Instance Type : t2.micro
State         : running
Key Pair      : xfusion-kp
```

---

# 13. Verify the Security Group

To verify the security group attached to the instance:

```bash
aws ec2 describe-instances \
    --filters "Name=tag:Name,Values=xfusion-ec2" \
    --region us-east-1 \
    --query 'Reservations[*].Instances[*].SecurityGroups[*].[GroupId,GroupName]' \
    --output table
```

The output should show the default security group.

---

# 14. Verify the Complete Instance

The complete instance information can be displayed using:

```bash
aws ec2 describe-instances \
    --filters "Name=tag:Name,Values=xfusion-ec2" \
    --region us-east-1 \
    --query 'Reservations[*].Instances[*].[InstanceId,InstanceType,State.Name,KeyName,SecurityGroups[*].GroupName]' \
    --output table
```

---

# 15. Stop the Instance

If the lab requires stopping the instance after completing the task, first obtain its instance ID:

```bash
aws ec2 describe-instances \
    --filters "Name=tag:Name,Values=xfusion-ec2" \
    --region us-east-1 \
    --query 'Reservations[*].Instances[*].InstanceId' \
    --output text
```

Then:

```bash
aws ec2 stop-instances \
    --instance-ids <INSTANCE-ID> \
    --region us-east-1
```

This step should only be performed if the lab requires the instance to be stopped.

---

# 16. Terminate the Instance

If cleanup is required after completing the lab:

```bash
aws ec2 terminate-instances \
    --instance-ids <INSTANCE-ID> \
    --region us-east-1
```

> **Warning:** Terminating an EC2 instance permanently removes the instance. Only perform this step when cleanup is required.

---

# Important Security Notes

Never commit AWS credentials or private keys to GitHub.

Do not commit:

```text
xfusion-kp.pem
*.pem
*.key
.env
AWS Access Key ID
AWS Secret Access Key
AWS Session Token
AWS Console Password
```

A `.gitignore` file can be used:

```gitignore
*.pem
*.key
.env
```

---

# Final Verification

The final EC2 configuration was:

```text
Instance Name : xfusion-ec2
AMI           : Amazon Linux
Instance Type : t2.micro
Key Pair      : xfusion-kp
Key Type      : RSA
Security Group: default
Region        : us-east-1
Status        : Running
```

---

# Task 06 Result

```text
AWS EC2
   |
   +-- Instance Name : xfusion-ec2
   |
   +-- AMI           : Amazon Linux
   |
   +-- Instance Type : t2.micro
   |
   +-- Key Pair      : xfusion-kp
   |
   +-- Key Type      : RSA
   |
   +-- Security Group: default
   |
   +-- Region        : us-east-1
   |
   +-- Status        : Successfully Created
```

**Task 06 — Completed Successfully **

100 Days. 100 Challenges. One Cloud Journey. ☁️🚀
