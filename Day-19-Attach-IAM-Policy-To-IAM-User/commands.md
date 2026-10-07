# Day 19 - IAM Policy Verification Commands

## 1. Verify AWS Identity

```bash
aws sts get-caller-identity
```

---

## 2. List IAM Users

```bash
aws iam list-users
```

---

## 3. List Policies Attached Directly to the User

Replace `<username>` with the IAM user's actual username.

```bash
aws iam list-attached-user-policies \
    --user-name <username>
```

---

## 4. Verify a Specific Attached Policy

Replace `<username>` with the IAM user's username and `<policy-name>` with the required policy name.

```bash
aws iam list-attached-user-policies \
    --user-name <username> \
    --query "AttachedPolicies[?PolicyName=='<policy-name>']"
```

---

The required policy should appear in the output.

---

# Verification Result

The expected result is that the required existing IAM policy appears in the list of policies attached directly to the IAM user.

```text
IAM User
   |
   +---- Existing IAM Policy
              |
              +---- Attached
```

**Day 19 — Verification Completed Successfully**
