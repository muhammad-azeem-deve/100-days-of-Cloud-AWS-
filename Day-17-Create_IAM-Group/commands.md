# Day 17 - IAM Group Verification Commands

These are the **AWS CLI verification commands** used to verify the IAM group created through the AWS Management Console.

---

## 1. List All IAM Groups

```bash
aws iam list-groups
```

---

## 2. List IAM Groups in Table Format

```bash
aws iam list-groups \
    --query 'Groups[*].[GroupName,GroupId,Arn]' \
    --output table
```

---

## 3. Verify the Required IAM Group

```bash
aws iam get-group \
    --group-name <GROUP_NAME>
```

---

## 4. Verify Only the Group Name

```bash
aws iam get-group \
    --group-name <GROUP_NAME> \
    --query 'Group.GroupName' \
    --output text
```

Expected output:

```text
<GROUP_NAME>
```

---

## 5. Verify Group Details

```bash
aws iam get-group \
    --group-name <GROUP_NAME> \
    --query 'Group.[GroupName,GroupId,Arn]' \
    --output table
```

---

## 6. Verify Group ARN

```bash
aws iam get-group \
    --group-name <GROUP_NAME> \
    --query 'Group.Arn' \
    --output text
```

---

## 7. Verify Group ID

```bash
aws iam get-group \
    --group-name <GROUP_NAME> \
    --query 'Group.GroupId' \
    --output text
```

---

## Final Verification

The required IAM group should return successfully from:

```bash
aws iam get-group --group-name <GROUP_NAME>
```

**Day 17 — IAM Group Verified Successfully**
