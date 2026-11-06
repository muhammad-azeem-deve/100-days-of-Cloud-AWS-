# Day 37 - AWS EC2 and S3 Verification Commands

This file contains **only AWS CLI verification commands** used for Day 37 of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

All AWS resources were created and configured through the **AWS Management Console**.

The following commands are used to verify the EC2 instance, IAM role, S3 bucket, and uploaded object.

---

# 1. Verify AWS CLI Version

```bash
aws --version
```

---

# 2. Verify AWS Identity on EC2

After connecting to the `xfusion-ec2` instance, I checked the identity being used by AWS CLI:

```bash
aws sts get-caller-identity
```

The returned ARN should indicate that the EC2 instance is using the attached IAM role.

Expected ARN format:

```text
arn:aws:sts::<ACCOUNT-ID>:assumed-role/xfusion-role/<SESSION-NAME>
```

This verifies that the instance is using temporary credentials from `xfusion-role`.

---

# 3. Verify the EC2 Instance

```bash
aws ec2 describe-instances \
    --filters "Name=tag:Name,Values=xfusion-ec2" \
    --region us-east-1 \
    --query 'Reservations[*].Instances[*].[InstanceId,State.Name,IamInstanceProfile.Arn]' \
    --output table
```

This verifies:

- EC2 instance identification.
- Instance running state.
- Attached IAM instance profile.

---

# 4. Verify the S3 Bucket

```bash
aws s3 ls s3://xfusion-s3-29734/
```

This command verifies that the EC2 instance can list objects in the S3 bucket.

---

# 5. Verify Bucket Location

```bash
aws s3api get-bucket-location \
    --bucket xfusion-s3-29734
```

This displays the bucket's AWS region.

---

# 6. Verify S3 Block Public Access

```bash
aws s3api get-public-access-block \
    --bucket xfusion-s3-29734
```

This verifies the bucket's public access blocking configuration.

All four settings should be enabled for this private bucket.

---

# 7. Verify the Uploaded File

```bash
aws s3api head-object \
    --bucket xfusion-s3-29734 \
    --key testfile.txt
```

A successful response confirms that the object exists and returns its metadata.

---

# 8. Verify Uploaded Objects

```bash
aws s3api list-objects-v2 \
    --bucket xfusion-s3-29734 \
    --query 'Contents[*].[Key,Size,LastModified]' \
    --output table
```

Expected object:

```text
testfile.txt
```

---

# 9. Verify IAM Role

Run this command from an AWS CLI environment with permission to inspect IAM roles:

```bash
aws iam get-role \
    --role-name xfusion-role
```

This verifies that the IAM role exists.

---

# 10. Verify Attached IAM Policies

```bash
aws iam list-attached-role-policies \
    --role-name xfusion-role
```

Expected attached policy:

```text
xfusion-s3-policy
```

---

# 11. Verify IAM Policy Details

```bash
aws iam get-policy \
    --policy-arn arn:aws:iam::<ACCOUNT-ID>:policy/xfusion-s3-policy
```

Replace `<ACCOUNT-ID>` with the actual AWS account ID.

---

# 12. Verify IAM Role Trust Relationship

```bash
aws iam get-role \
    --role-name xfusion-role \
    --query 'Role.AssumeRolePolicyDocument' \
    --output json
```

The trust relationship should allow the EC2 service to assume the role.

Expected service:

```text
ec2.amazonaws.com
```

---

# Important Notes

- Run the S3 access verification commands from inside `xfusion-ec2` to test the attached instance role.
- IAM inspection commands may require additional IAM permissions and can be run from the AWS client.
- Do not configure permanent AWS access keys on the EC2 instance.
- The IAM role should provide temporary credentials automatically.

---

# Final Verification Result

```text
EC2 Instance       : xfusion-ec2
S3 Bucket          : xfusion-s3-29734
IAM Policy         : xfusion-s3-policy
IAM Role           : xfusion-role
S3 Upload          : Verified
S3 Object Listing  : Verified
IAM Role Access    : Verified
```

**Day 37 — Completed Successfully ✅**

100 Days. 100 Challenges. One Cloud Journey. ☁️🚀
