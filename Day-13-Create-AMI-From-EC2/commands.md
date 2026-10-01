# Day 13 - AMI Verification Commands

This file contains **verification commands only** for Day 13 of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The AMI was created using the **AWS Management Console**.

These AWS CLI commands were used only to verify the created AMI.

---

# 1. Verify AWS Identity

```bash
aws sts get-caller-identity
```

---

# 2. Verify the AWS Region

The challenge required the resource to be created in:

```text
us-east-1
```

The region can be explicitly specified with the AWS CLI commands:

```bash
--region us-east-1
```

---

# 3. List AMIs Owned by the Account

To list AMIs owned by the current AWS account:

```bash
aws ec2 describe-images \
    --owners self \
    --region us-east-1
```

---

# 4. Display AMI ID, Name, and State

To verify the available AMIs in a simple table:

```bash
aws ec2 describe-images \
    --owners self \
    --region us-east-1 \
    --query 'Images[*].[ImageId,Name,State]' \
    --output table
```

---

# 5. Verify Available AMIs

To display only AMIs whose state is `available`:

```bash
aws ec2 describe-images \
    --owners self \
    --filters Name=state,Values=available \
    --region us-east-1 \
    --query 'Images[*].[ImageId,Name,State]' \
    --output table
```

---


# 6. Verify AMI Details

To display important AMI information:

```bash
aws ec2 describe-images \
    --image-ids <AMI-ID> \
    --region us-east-1 \
    --query 'Images[0].[ImageId,Name,State,Architecture,RootDeviceType]' \
    --output table
```

---


# 7. Final Verification

The AMI creation was considered successful when the AMI state returned:

```text
available
```

The main verification command was:

```bash
aws ec2 describe-images \
    --image-ids <AMI-ID> \
    --region us-east-1 \
    --query 'Images[0].[ImageId,Name,State]' \
    --output table
```

Expected state:

```text
available
```

---

# Day 8 Result

```text
AMI Created       : Yes
AMI State         : available
AWS Region        : us-east-1
Creation Method   : AWS Console
Verification      : AWS Console + AWS CLI
Status             : Completed Successfully
```

**Day 9 — Completed Successfully**
