# Day 16 - IAM User Verification Commands

## Verify AWS Identity

```bash
aws sts get-caller-identity
```

---

## List IAM Users

```bash
aws iam list-users
```

---

## List IAM User Names Only

```bash
aws iam list-users \
    --query 'Users[*].UserName' \
    --output table
```

---

## Verify Specific IAM User

```bash
aws iam get-user \
    --user-name <IAM_USERNAME>
```

---

## Verify User Name

```bash
aws iam get-user \
    --user-name <IAM_USERNAME> \
    --query 'User.UserName' \
    --output text
```

---

## Verify User ID

```bash
aws iam get-user \
    --user-name <IAM_USERNAME> \
    --query 'User.UserId' \
    --output text
```

---


## Final Verification

```bash
aws iam get-user --user-name <IAM_USERNAME>
```

The command should return the details of the IAM user created through the AWS Management Console.

**Day 16 — IAM User Verification Completed Successfully**
