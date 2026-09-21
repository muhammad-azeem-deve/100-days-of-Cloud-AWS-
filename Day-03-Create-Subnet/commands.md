# Day 03 - AWS Subnet Commands

This file contains the AWS CLI commands used for **Day 03** of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The task was to create one subnet named `devops-subnet` under the **default VPC** in the **us-east-1** region and verify it using `describe-subnets` with a `vpc-id` filter.

---

# 1. Retrieve KodeKloud AWS Credentials

The KodeKloud AWS client provides the `showcreds` command for retrieving temporary lab credentials:

```bash
showcreds
```

The credentials returned by this command should only be used for the lab environment.

> **Security Note:** Never add AWS credentials, passwords, access keys, or session tokens to GitHub.

---

# 2. Verify AWS CLI

First, I checked whether the AWS CLI was available:

```bash
aws --version
```

---

# 3. Verify AWS Identity

After configuring the temporary credentials, I verified the AWS identity:

```bash
aws sts get-caller-identity
```

This confirms that the AWS CLI is authenticated with the lab account.

---

# 4. Verify the Required Region

The challenge required the resources to be created only in:

```text
us-east-1
```

Therefore, I explicitly specified the region in the AWS CLI commands:

```bash
--region us-east-1
```

---

# 5. Find the Default VPC

I retrieved the default VPC using:

```bash
aws ec2 describe-vpcs \
    --region us-east-1 \
    --filters Name=isDefault,Values=true
```

To display only the VPC ID:

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

I stored this value as the default VPC ID for the remaining commands.

---

# 6. Check the Default VPC CIDR

I checked the CIDR block of the default VPC:

```bash
aws ec2 describe-vpcs \
    --region us-east-1 \
    --filters Name=isDefault,Values=true \
    --query 'Vpcs[0].[VpcId,CidrBlock]' \
    --output table
```

Example:

```text
------------------------------------------------
|                DescribeVpcs                  |
+----------------------+-----------------------+
| vpc-xxxxxxxxxxxxxxxx | 172.31.0.0/16        |
+----------------------+-----------------------+
```

The actual CIDR block depends on the lab environment.

---

# 7. Check Existing Subnets

Before creating the new subnet, I checked the existing subnets in the default VPC:

```bash
aws ec2 describe-subnets \
    --region us-east-1 \
    --filters Name=vpc-id,Values=<DEFAULT_VPC_ID>
```

This was important to avoid selecting a CIDR block that overlaps with an existing subnet.

---

# 8. Display Existing Subnet CIDRs

To display the existing subnet CIDR blocks:

```bash
aws ec2 describe-subnets \
    --region us-east-1 \
    --filters Name=vpc-id,Values=<DEFAULT_VPC_ID> \
    --query 'Subnets[*].[SubnetId,CidrBlock,AvailabilityZone]' \
    --output table
```

I used this information to select an available CIDR block for the new subnet.

---

# 9. Create the Subnet

The required subnet name was:

```text
devops-subnet
```

The general command used to create it was:

```bash
aws ec2 create-subnet \
    --vpc-id <DEFAULT_VPC_ID> \
    --cidr-block <AVAILABLE_CIDR_BLOCK> \
    --region us-east-1 \
    --tag-specifications 'ResourceType=subnet,Tags=[{Key=Name,Value=devops-subnet}]'
```

For example, if the selected CIDR was:

```text
172.31.32.0/20
```

the command would be:

```bash
aws ec2 create-subnet \
    --vpc-id <DEFAULT_VPC_ID> \
    --cidr-block 172.31.32.0/20 \
    --region us-east-1 \
    --tag-specifications 'ResourceType=subnet,Tags=[{Key=Name,Value=devops-subnet}]'
```

The CIDR value should be replaced with the available CIDR block identified in the lab.

---

# 10. Verify Using vpc-id Filter

The challenge specifically required verification using the VPC ID filter.

I used:

```bash
aws ec2 describe-subnets \
    --region us-east-1 \
    --filters Name=vpc-id,Values=<DEFAULT_VPC_ID>
```

This displays the subnets associated with the default VPC.

---

# 11. Verify the devops-subnet

To find the specific subnet by both VPC ID and subnet name:

```bash
aws ec2 describe-subnets \
    --region us-east-1 \
    --filters \
        Name=vpc-id,Values=<DEFAULT_VPC_ID> \
        Name=tag:Name,Values=devops-subnet
```

A successful response confirms that the subnet exists under the required VPC.

---

# 12. Display Subnet Details

I used the following command to display the important information in table format:

```bash
aws ec2 describe-subnets \
    --region us-east-1 \
    --filters Name=vpc-id,Values=<DEFAULT_VPC_ID> \
    --query 'Subnets[*].[SubnetId,VpcId,CidrBlock,AvailabilityZone,Tags[?Key==`Name`].Value|[0]]' \
    --output table
```

The output provides:

```text
Subnet ID
VPC ID
CIDR Block
Availability Zone
Subnet Name
```

---

# 13. Verify Only devops-subnet

For a focused verification:

```bash
aws ec2 describe-subnets \
    --region us-east-1 \
    --filters \
        Name=vpc-id,Values=<DEFAULT_VPC_ID> \
        Name=tag:Name,Values=devops-subnet \
    --query 'Subnets[*].[SubnetId,VpcId,CidrBlock,AvailabilityZone]' \
    --output table
```

Expected information:

```text
devops-subnet
     |
     +-- VPC: Default VPC
     |
     +-- Region: us-east-1
     |
     +-- CIDR: <selected-available-cidr>
```

---

# 14. Optional Cleanup

If the lab requires the subnet to be removed after completing the task, first retrieve its subnet ID:

```bash
aws ec2 describe-subnets \
    --region us-east-1 \
    --filters \
        Name=vpc-id,Values=<DEFAULT_VPC_ID> \
        Name=tag:Name,Values=devops-subnet \
    --query 'Subnets[0].SubnetId' \
    --output text
```

Then delete it:

```bash
aws ec2 delete-subnet \
    --subnet-id <SUBNET_ID> \
    --region us-east-1
```

This cleanup should only be performed if required by the lab.

---

# Important Security Notes

Never commit temporary AWS credentials to GitHub.

Do not commit:

```text
AWS Access Key ID
AWS Secret Access Key
AWS Session Token
AWS Console Password
```

If credentials are stored in environment files, add them to `.gitignore`:

```gitignore
.env
*.pem
*.key
```

---

# Final Verification

The final resource configuration was:

```text
Subnet Name : devops-subnet
VPC         : Default VPC
Region      : us-east-1
Status      : Successfully Created
```

The required verification command was:

```bash
aws ec2 describe-subnets \
    --region us-east-1 \
    --filters Name=vpc-id,Values=<DEFAULT_VPC_ID>
```

---

# Day 03 Result

```text
AWS VPC
   |
   +-- Default VPC
          |
          +-- devops-subnet
                  |
                  +-- Region: us-east-1
                  |
                  +-- Status: Created Successfully
```

**Day 03 — Completed Successfully **

100 Days. 100 Challenges. One Cloud Journey. ☁️🚀
