# Day 18 - IAM Policy Verification Commands

This file contains **verification commands only** for Day 18 of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The IAM policy was created using the **AWS Management Console**.

The AWS CLI was used only to verify the policy.

---

# 1. Verify AWS Identity

First, I verified the AWS account and identity being used:

```bash
aws sts get-caller-identity
```

---

# 2. List IAM Policies

To list customer-managed IAM policies:

```bash
aws iam list-policies \
    --scope Local \
    --output table
```

---

# 3. Find the EC2 Read-Only Policy

To find the policy by name:

```bash
aws iam list-policies \
    --scope Local \
    --query "Policies[?PolicyName=='EC2ReadOnlyPolicy'].[PolicyName,Arn,PolicyId,DefaultVersionId]" \
    --output table
```

---

# 4. Get Policy Details

To verify the policy details:

```bash
aws iam get-policy \
    --policy-arn arn:aws:iam::$(aws sts get-caller-identity --query Account --output text):policy/EC2ReadOnlyPolicy
```

This verifies information such as:

```text
PolicyName
PolicyId
Arn
DefaultVersionId
AttachmentCount
CreateDate
UpdateDate
```

---

# 5. Get the Policy Version

To retrieve the default policy version:

```bash
aws iam get-policy-version \
    --policy-arn arn:aws:iam::$(aws sts get-caller-identity --query Account --output text):policy/EC2ReadOnlyPolicy \
    --version-id v1
```

---

# 6. Display the Policy Document

To display only the policy document:

```bash
aws iam get-policy-version \
    --policy-arn arn:aws:iam::$(aws sts get-caller-identity --query Account --output text):policy/EC2ReadOnlyPolicy \
    --version-id v1 \
    --query 'PolicyVersion.Document'
```

---

# 7. Verify EC2 Read Permissions

To display the actions allowed by the policy:

```bash
aws iam get-policy-version \
    --policy-arn arn:aws:iam::$(aws sts get-caller-identity --query Account --output text):policy/EC2ReadOnlyPolicy \
    --version-id v1 \
    --query 'PolicyVersion.Document.Statement[].Action'
```

Expected permissions include read-only EC2 actions such as:

```text
ec2:DescribeInstances
ec2:DescribeImages
ec2:DescribeVolumes
ec2:DescribeSnapshots
ec2:DescribeSecurityGroups
ec2:DescribeKeyPairs
ec2:DescribeNetworkInterfaces
ec2:DescribeSubnets
ec2:DescribeVpcs
```

---

# 8. Verify Policy Effect

To check the policy effect:

```bash
aws iam get-policy-version \
    --policy-arn arn:aws:iam::$(aws sts get-caller-identity --query Account --output text):policy/EC2ReadOnlyPolicy \
    --version-id v1 \
    --query 'PolicyVersion.Document.Statement[].Effect'
```

Expected result:

```text
Allow
```

---

# 9. Verify Policy Resource

To verify the resources covered by the policy:

```bash
aws iam get-policy-version \
    --policy-arn arn:aws:iam::$(aws sts get-caller-identity --query Account --output text):policy/EC2ReadOnlyPolicy \
    --version-id v1 \
    --query 'PolicyVersion.Document.Statement[].Resource'
```

Expected result:

```text
*
```

---

# 10. Check EC2 Read-Only Actions

A compact verification command:

```bash
aws iam get-policy-version \
    --policy-arn arn:aws:iam::$(aws sts get-caller-identity --query Account --output text):policy/EC2ReadOnlyPolicy \
    --version-id v1 \
    --query 'PolicyVersion.Document.Statement[].Action' \
    --output json
```

This can be used to verify that the policy contains EC2 read actions.

---

# 11. Final Verification

The final policy can be verified using:

```bash
aws iam get-policy \
    --policy-arn arn:aws:iam::$(aws sts get-caller-identity --query Account --output text):policy/EC2ReadOnlyPolicy
```

Then:

```bash
aws iam get-policy-version \
    --policy-arn arn:aws:iam::$(aws sts get-caller-identity --query Account --output text):policy/EC2ReadOnlyPolicy \
    --version-id v1
```

---

# Day 18 Verification Result

```text
Policy Name  : EC2ReadOnlyPolicy
Policy Type  : Customer Managed
Service      : Amazon EC2
Access Level : Read
Effect       : Allow
Status       : Successfully Verified
```

**Day 18 — Completed Successfully**
