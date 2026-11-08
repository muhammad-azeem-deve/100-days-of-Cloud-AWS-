# Day 39 - AWS S3 Static Website Verification Commands

This file contains **only AWS CLI verification commands** used for Day 39 of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

All implementation steps, including bucket creation, public access configuration, static website hosting, bucket policy configuration, and file upload, were performed using the **AWS Management Console**.

The following commands are only for verifying the completed configuration.

---

## 1. Verify AWS Identity

Check the AWS account currently connected to the CLI:

```bash
aws sts get-caller-identity
```

---

## 2. Verify S3 Bucket Existence

Check whether the required bucket exists:

```bash
aws s3api head-bucket \
    --bucket <your-bucket-name>
```

A successful command returns no output and an exit status of `0`.

---

## 3. Verify Bucket Region

Check the region in which the S3 bucket was created:

```bash
aws s3api get-bucket-location \
    --bucket <your-bucket-name>
```

Expected region:

```text
us-east-1
```

---

## 4. Verify Block Public Access Configuration

Check the bucket's public access block settings:

```bash
aws s3api get-public-access-block \
    --bucket <your-bucket-name>
```

The expected configuration has all four settings set to `false`:

```json
{
    "BlockPublicAcls": false,
    "IgnorePublicAcls": false,
    "BlockPublicPolicy": false,
    "RestrictPublicBuckets": false
}
```

---

## 5. Verify Static Website Hosting

Check whether static website hosting is enabled:

```bash
aws s3api get-bucket-website \
    --bucket <your-bucket-name>
```

Expected configuration:

```json
{
    "IndexDocument": {
        "Suffix": "index.html"
    }
}
```

This confirms that `index.html` is configured as the index document.

---

## 6. Verify Uploaded Website Files

List the objects stored inside the S3 bucket:

```bash
aws s3api list-objects-v2 \
    --bucket <your-bucket-name>
```

The output should contain:

```text
index.html
```

Alternatively, use:

```bash
aws s3 ls s3://<your-bucket-name>/
```

---

## 7. Verify the index.html Object

Check whether the uploaded HTML file exists:

```bash
aws s3api head-object \
    --bucket <your-bucket-name> \
    --key index.html
```

This command displays the object's metadata, including its content length and last modified date.

---

## 8. Verify Public Bucket Policy Status

Check whether the bucket policy makes the bucket publicly accessible:

```bash
aws s3api get-bucket-policy-status \
    --bucket <your-bucket-name>
```

Expected result:

```json
{
    "PolicyStatus": {
        "IsPublic": true
    }
}
```

---

## 9. Verify Bucket Policy

Display the bucket policy:

```bash
aws s3api get-bucket-policy \
    --bucket <your-bucket-name>
```

Verify that the policy contains:

```text
Effect    : Allow
Principal : *
Action    : s3:GetObject
Resource  : arn:aws:s3:::<your-bucket-name>/*
```

---

## 10. Verify Static Website Endpoint

The S3 website endpoint for the `us-east-1` region follows this format:

```text
http://<your-bucket-name>.s3-website-us-east-1.amazonaws.com
```

Check website accessibility using:

```bash
curl -I http://<your-bucket-name>.s3-website-us-east-1.amazonaws.com
```

A successful response should return:

```text
HTTP/1.1 200 OK
```

To verify the actual website content:

```bash
curl http://<your-bucket-name>.s3-website-us-east-1.amazonaws.com
```

The returned HTML should contain the website content, including:

```text
Welcome to KKE labs!
```

---

## Final Verification Summary

| Verification | Expected Result |
| ------------------------- | ---------------- |
| AWS Identity | Successfully Connected |
| S3 Bucket | Exists |
| AWS Region | `us-east-1` |
| Block Public Access | Disabled |
| Static Website Hosting | Enabled |
| Index Document | `index.html` |
| Uploaded Object | `index.html` |
| Bucket Policy | Public Read |
| Website Endpoint | HTTP 200 OK |

---

## Day 39 Result

```text
S3 Bucket          : <your-bucket-name>
Region             : us-east-1
Static Hosting     : Enabled
Index Document     : index.html
Public Access      : Configured
Website Status     : Accessible
Challenge Status   : Completed Successfully
```

**Day 39 — Completed Successfully ✅**

100 Days. 100 Challenges. One Cloud Journey. ☁️🚀
