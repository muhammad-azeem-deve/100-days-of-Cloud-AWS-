
# Day 28 - AWS ECR Verification Commands

This file contains only the verification commands used for Day 28 of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The task was to create a private Amazon ECR repository, push a Docker image, and verify the uploaded resources.

**AWS Region:** `us-east-1`

Replace `<repository-name>` with the actual repository name created in the lab.

---

## 1. Verify AWS CLI Version

```bash
aws --version
```

---

## 2. Verify AWS Identity

```bash
aws sts get-caller-identity
```

This verifies the AWS identity associated with the current CLI credentials.

---

## 3. Verify the AWS Region

```bash
aws configure get region
```

The expected configured region is:

```text
us-east-1
```

Note: This checks the configured default region. Individual commands below explicitly specify `us-east-1`.

---

## 4. List ECR Repositories

```bash
aws ecr describe-repositories \
  --region us-east-1 \
  --query 'repositories[*].[repositoryName,repositoryUri]' \
  --output table
```

This lists the ECR repositories available to the current AWS identity in the specified region.

---

## 5. Verify the Required Repository

```bash
aws ecr describe-repositories \
  --region us-east-1 \
  --repository-names <repository-name>
```

This verifies that the specified repository exists.

---

## 6. Verify Repository Visibility

```bash
aws ecr describe-repositories \
  --region us-east-1 \
  --repository-names <repository-name> \
  --query 'repositories[0].[repositoryName,repositoryArn,repositoryUri,registryId]' \
  --output table
```

This displays the repository name, ARN, URI, and registry ID.

The AWS Console can be used to confirm that the repository visibility is set to **Private**.

---

## 7. Verify Uploaded Images

```bash
aws ecr describe-images \
  --region us-east-1 \
  --repository-name <repository-name>
```

This displays information about the images stored in the repository.

---

## 8. Verify Image Tags and Digests

```bash
aws ecr describe-images \
  --region us-east-1 \
  --repository-name <repository-name> \
  --query 'imageDetails[*].[imageTags,imageDigest]' \
  --output table
```

This verifies the available image tags and their associated digests.

---

## 9. Verify Image Push Time

```bash
aws ecr describe-images \
  --region us-east-1 \
  --repository-name <repository-name> \
  --query 'imageDetails[*].[imageTags,imagePushedAt,imageSizeInBytes]' \
  --output table
```

This displays the image tags, push timestamps, and image sizes.

---

## 10. Verify a Specific Image Tag

If the image was pushed with the `latest` tag:

```bash
aws ecr describe-images \
  --region us-east-1 \
  --repository-name <repository-name> \
  --image-ids imageTag=latest
```

Replace `latest` with the actual tag if a different tag was used.

---

## 11. Verify the Local Docker Image

```bash
docker images
```

This checks the images available in the local Docker environment.

---

## 12. Verify Docker Image Details

```bash
docker image inspect <local-image-name>:latest
```

Replace `<local-image-name>` with the actual local Docker image name.

This displays metadata about the local image.

---

## 13. Final Verification

```bash
aws ecr describe-repositories \
  --region us-east-1 \
  --repository-names <repository-name>
```

```bash
aws ecr describe-images \
  --region us-east-1 \
  --repository-name <repository-name> \
  --query 'imageDetails[*].[imageTags,imageDigest,imagePushedAt]' \
  --output table
```

**Day 28 — Verification Commands Completed ✅**
