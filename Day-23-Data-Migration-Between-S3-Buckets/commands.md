# Day 23 - S3 Data Migration Verification Commands

This file contains AWS CLI commands for verifying **Day 23** of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The data migration was performed through the AWS Management Console. These commands are used only to verify the source bucket, destination bucket, and migrated objects.

## 1. Verify AWS CLI Availability

```bash
aws --version
```

## 2. Verify the Active AWS Identity

```bash
aws sts get-caller-identity
```

## 3. Verify the Source Bucket Exists

Replace `<SOURCE_BUCKET>` with the actual source bucket name.

```bash
aws s3api head-bucket \
    --bucket <SOURCE_BUCKET> \
    --region us-east-1
```

A successful command normally returns no output and exits with status code `0`.

## 4. Verify the Destination Bucket Exists

Replace `<DESTINATION_BUCKET>` with the actual destination bucket name.

```bash
aws s3api head-bucket \
    --bucket <DESTINATION_BUCKET> \
    --region us-east-1
```

## 5. List Objects in the Source Bucket

```bash
aws s3 ls s3://<SOURCE_BUCKET>/ \
    --region us-east-1 \
    --recursive
```

## 6. List Objects in the Destination Bucket

```bash
aws s3 ls s3://<DESTINATION_BUCKET>/ \
    --region us-east-1 \
    --recursive
```

## 7. Verify Destination Object Metadata

Replace `<OBJECT_KEY>` with the exact object key, including any folder prefixes.

```bash
aws s3api head-object \
    --bucket <DESTINATION_BUCKET> \
    --key "<OBJECT_KEY>" \
    --region us-east-1
```

This displays metadata such as the object size, ETag, and last-modified timestamp.

## 8. Verify Destination Object Names

```bash
aws s3api list-objects-v2 \
    --bucket <DESTINATION_BUCKET> \
    --region us-east-1 \
    --query 'Contents[].Key' \
    --output table
```

## 9. Verify Destination Object Count

```bash
aws s3api list-objects-v2 \
    --bucket <DESTINATION_BUCKET> \
    --region us-east-1 \
    --query 'length(Contents[])' \
    --output text
```

**Note:** This count covers the objects returned by that request, up to 1,000 objects. For buckets containing more than 1,000 objects, use the recursive `aws s3 ls` command or another paginated verification method.

## 10. Compare Source and Destination Object Listings

```bash
aws s3 ls s3://<SOURCE_BUCKET>/ \
    --region us-east-1 \
    --recursive
```

```bash
aws s3 ls s3://<DESTINATION_BUCKET>/ \
    --region us-east-1 \
    --recursive
```

Compare the output with the objects required by the lab. The source and destination may contain different additional objects, so verify the specific objects that were required to be migrated.

---

## Verification Result

```text
Source Bucket      : Verified
Destination Bucket : Verified
Migrated Objects   : Verify against lab requirements
Method             : AWS CLI verification
Region             : us-east-1
```

**Day 23 — S3 Data Migration Verification Complete**

> Security reminder: Never commit AWS credentials, session tokens, or other secrets to GitHub.
