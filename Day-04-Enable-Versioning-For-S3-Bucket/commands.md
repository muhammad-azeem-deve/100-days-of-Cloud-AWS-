# Day 04 - S3 Versioning Commands

This file contains the AWS CLI commands used for **Day 04** of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The task was to enable versioning on the following S3 bucket:

```text
xfusion-s3-409051293
```

The required AWS region was:

```text
us-east-1
```

---

# 1. Check AWS CLI

First, I checked whether the AWS CLI was installed:

```bash
aws --version
```

---

# 2. Retrieve KodeKloud Lab Credentials

The KodeKloud AWS client provides the `showcreds` command to retrieve the temporary AWS credentials:

```bash
showcreds
```

These credentials are temporary and should only be used within the lab environment.

> **Security Note:** Never add AWS credentials, passwords, access keys, secret keys, or session tokens to GitHub.

---

# 3. Verify AWS Identity

I verified the AWS account associated with the current credentials:

```bash
aws sts get-caller-identity
```

This confirms that the AWS CLI is connected to the KodeKloud lab environment.

---

# 4. Set the Required Region

The challenge required the S3 bucket to be managed in:

```text
us-east-1
```

The region can be explicitly specified in AWS CLI commands using:

```bash
--region us-east-1
```

---

# 5. Check the S3 Bucket



A successful command indicates that the bucket exists and is accessible using the current AWS credentials.

---

# 6. Check Current Versioning Status

Before making the change, I checked the current versioning configuration:

```bash
aws s3api get-bucket-versioning \
    --bucket xfusion-s3-409051293 \
    --region us-east-1
```

This command displays the current versioning status of the bucket.

Possible results include:

```json
{
    "Status": "Enabled"
}
```

or, if versioning has not been enabled:

```text
No output
```

---

# 7. Enable S3 Versioning

I enabled versioning using the following AWS CLI command:

```bash
aws s3api put-bucket-versioning \
    --bucket xfusion-s3-409051293 \
    --versioning-configuration Status=Enabled \
    --region us-east-1
```

The command enables versioning for:

```text
Bucket : xfusion-s3-409051293
Region : us-east-1
```

---

# 8. Verify Versioning

After enabling versioning, I checked the configuration again:

```bash
aws s3api get-bucket-versioning \
    --bucket xfusion-s3-409051293 \
    --region us-east-1
```

Expected result:

```json
{
    "Status": "Enabled"
}
```

This confirmed that S3 Versioning was successfully enabled.

---

# 9. Check Bucket Location

The bucket's AWS region can also be checked using:

```bash
aws s3api get-bucket-location \
    --bucket xfusion-s3-409051293
```

The bucket was required to be in:

```text
us-east-1
```

---

# 10. List the S3 Bucket

The bucket can be viewed using:

```bash
aws s3 ls s3://xfusion-s3-409051293 \
    --region us-east-1
```

This lists the objects currently stored in the bucket.

---

# 11. Final Verification Command

The most important verification command for this task was:

```bash
aws s3api get-bucket-versioning \
    --bucket xfusion-s3-409051293 \
    --region us-east-1
```

Expected output:

```json
{
    "Status": "Enabled"
}
```

---

# Command Summary

The main commands used in this challenge were:

### Check AWS CLI

```bash
aws --version
```

### Get AWS identity

```bash
aws sts get-caller-identity
```


### Check versioning

```bash
aws s3api get-bucket-versioning \
    --bucket xfusion-s3-409051293 \
    --region us-east-1
```

### Enable versioning

```bash
aws s3api put-bucket-versioning \
    --bucket xfusion-s3-409051293 \
    --versioning-configuration Status=Enabled \
    --region us-east-1
```

### Verify versioning

```bash
aws s3api get-bucket-versioning \
    --bucket xfusion-s3-409051293 \
    --region us-east-1
```

---

# Final Result

```text
S3 Bucket    : xfusion-s3-409051293
Region       : us-east-1
Versioning   : Enabled
Status       : Successfully Completed
```

# Day 04 Result

**Day 04 — Completed Successfully **

**Enabled S3 Versioning on** **`xfusion-s3-409051293`** **and verified that the bucket versioning status is** **`Enabled`**.

100 Days. 100 Challenges. One Cloud Journey. ☁️🚀
