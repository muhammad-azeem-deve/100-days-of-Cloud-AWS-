# Day 10 - IAM Role Verification Commands

This file contains **AWS CLI verification commands only** for Day 10 of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The IAM role was created and configured using the **AWS Management Console**.

These commands are used only to verify the completed configuration.

---

# 1. Verify AWS Identity

First, verify that the AWS CLI is connected to the KodeKloud lab account:

```bash
aws sts get-caller-identity
```

---

# 2. List IAM Roles

To verify that the IAM role exists:

```bash
aws iam list-roles
```

---

# 3. Find the Created Role

To display IAM roles in a readable format:

```bash
aws iam list-roles \
    --query 'Roles[*].[RoleName,Arn]' \
    --output table
```

Locate the role created for the EC2 service.

---

# 4. Get IAM Role Details

Replace `<ROLE_NAME>` with the role name created during the lab:

```bash
aws iam get-role \
    --role-name <ROLE_NAME>
```

---

# 5. Verify the Trust Relationship

To verify which entity can assume the role:

```bash
aws iam get-role \
    --role-name <ROLE_NAME> \
    --query 'Role.AssumeRolePolicyDocument'
```

The trust policy should contain the EC2 service:

```text
ec2.amazonaws.com
```

---

# 6. Verify Attached Policies

To list policies attached directly to the role:

```bash
aws iam list-attached-role-policies \
    --role-name <ROLE_NAME>
```

---


### Trust Relationship

```bash
aws iam get-role \
    --role-name <ROLE_NAME> \
    --query 'Role.AssumeRolePolicyDocument'
```

### Attached Policies

```bash
aws iam list-attached-role-policies \
    --role-name <ROLE_NAME>
```

### Inline Policies

```bash
aws iam list-role-policies \
    --role-name <ROLE_NAME>
```

---

# Expected Verification

The completed IAM role should have:

```text
IAM Role
   |
   +-- Exists
   |
   +-- Trusted Service: EC2
   |
   +-- Trust Principal: ec2.amazonaws.com
   |
   +-- Required Policy: Attached
   |
   +-- Status: Successfully Configured
```

---

# Day 10 Result

```text
IAM Role Verification
        |
        +-- Role exists             ✅
        |
        +-- EC2 trusted entity      ✅
        |
        +-- Trust relationship      ✅
        |
        +-- Required policy         ✅
        |
        +-- AWS CLI verification    ✅
```

**Day 10 — Completed Successfully**
