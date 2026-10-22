
# Day 28 - Create a Private AWS ECR Repository and Push Docker Image

## Challenge Overview

This is Day 28 of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The challenge was related to **Amazon Elastic Container Registry (ECR), Docker Image Building, and Container Image Management**.

The Nautilus DevOps team needed a private repository to store a Docker image in AWS. The lab environment already contained a `Dockerfile`, which was required to build a Docker image and push it to the newly created private Amazon ECR repository.

The task involved creating the repository through the AWS Management Console, building the Docker image using the provided Dockerfile, and uploading the image to Amazon ECR.

### Challenge Requirement

The requirements were:

1. Access the AWS Management Console using the KodeKloud lab environment.
2. Use the `us-east-1` AWS region.
3. Create a private Amazon ECR repository.
4. Locate the provided Dockerfile in the lab environment.
5. Build a Docker image using the Dockerfile.
6. Authenticate Docker with Amazon ECR.
7. Tag the image using the ECR repository URI.
8. Push the Docker image to the private ECR repository.
9. Verify the repository and uploaded image using the AWS Console.
10. Verify the repository and image using AWS CLI commands.

---

## Objectives

The objectives of this challenge are:

- Understand Amazon Elastic Container Registry (ECR).
- Create a private ECR repository using the AWS Console.
- Understand the role of a Dockerfile in building container images.
- Build a Docker image from an existing Dockerfile.
- Understand Docker image tagging.
- Authenticate Docker with a private ECR registry.
- Push a Docker image to AWS.
- Verify uploaded images through the AWS Console and AWS CLI.
- Understand how private container images are stored and managed in AWS.

---

## Environment

| Item | Details |
|---|---|
| Challenge | 100 Days of Cloud (AWS) |
| Platform | KodeKloud |
| Day | 28 |
| Cloud Provider | Amazon Web Services |
| AWS Service | Amazon Elastic Container Registry (ECR) |
| AWS Region | `us-east-1` |
| Repository Visibility | Private |
| Source File | `Dockerfile` provided in the lab |
| Container Tool | Docker |
| Image Destination | Private Amazon ECR repository |
| Verification | AWS Console and AWS CLI |
| Status | Completed |

---

# Solution

## Step 1: Access the AWS Management Console

First, I accessed the AWS Management Console using the temporary credentials provided by the KodeKloud lab.

I selected the required AWS region:

```text
us-east-1
```

All AWS ECR operations for this challenge were performed in the specified region.

---

## Step 2: Open Amazon ECR

From the AWS Management Console, I searched for:

```text
Elastic Container Registry
```

I opened the Amazon ECR service and navigated to the private repositories section.

The navigation path was:

```text
AWS Management Console
        |
        v
Amazon ECR
        |
        v
Private registry
        |
        v
Repositories
```

Amazon ECR is an AWS service used to store, manage, and distribute container images.

---

## Step 3: Create a Private ECR Repository

I selected **Create repository**.

I configured the repository with the following settings:

```text
Visibility settings: Private
Repository name: <repository-name>
AWS Region: us-east-1
```

I used the repository name required by the lab, if specified.

I kept the remaining settings at their appropriate defaults unless the lab required otherwise.

After configuring the repository, I selected **Create repository**.

The repository was now available in my private Amazon ECR registry.

---

## Step 4: Open the Lab Terminal

Next, I accessed the AWS client terminal provided by the KodeKloud lab.

I located the Dockerfile supplied with the lab.

I checked the current directory:

```bash
pwd
```

I listed the available files:

```bash
ls -la
```

If the Dockerfile was located in another directory, I navigated to that directory before building the image.

I verified that the Dockerfile was present:

```bash
ls -l Dockerfile
```

The Dockerfile contains the instructions Docker uses to build a container image.

---

## Step 5: Build the Docker Image

From the directory containing the provided Dockerfile, I built the Docker image.

The general command was:

```bash
docker build -t <local-image-name>:latest .
```

Here:

- `docker build` builds a Docker image.
- `-t` assigns a name and tag to the image.
- `<local-image-name>` represents the chosen local image name.
- `latest` is the image tag.
- `.` specifies the current directory as the build context.

Docker read the Dockerfile and created the image according to its instructions.

I used the image name required by the lab, if one was specified.

---

## Step 6: Verify the Local Docker Image

Before uploading the image, I checked that it had been built successfully.

```bash
docker images
```

I confirmed that the newly built image appeared in the local Docker image list.

This step helped ensure that the image was available before pushing it to AWS.

---

## Step 7: Obtain the ECR Repository URI

I returned to the Amazon ECR Console.

I opened the repository created in Step 3 and copied its repository URI.

The URI follows this general format:

```text
<aws-account-id>.dkr.ecr.us-east-1.amazonaws.com/<repository-name>
```

The actual URI depends on the AWS account and repository name provided by the lab.

I used the exact URI displayed by the AWS Console.

---

## Step 8: Authenticate Docker with Amazon ECR

Before pushing the image, Docker needed to authenticate with the private ECR registry.

The AWS CLI supports ECR authentication using an authorization token.

The general authentication command is:

```bash
aws ecr get-login-password --region us-east-1 \
  | docker login \
      --username AWS \
      --password-stdin <aws-account-id>.dkr.ecr.us-east-1.amazonaws.com
```

If authentication succeeded, Docker displayed:

```text
Login Succeeded
```

This authentication allowed Docker to communicate with the private ECR registry, subject to the permissions of the lab's AWS identity.

---

## Step 9: Tag the Docker Image

I tagged the locally built Docker image with the ECR repository URI.

The general command was:

```bash
docker tag <local-image-name>:latest \
  <aws-account-id>.dkr.ecr.us-east-1.amazonaws.com/<repository-name>:latest
```

This associated the local image with its destination in Amazon ECR.

The repository URI and image tag must match the intended destination.

---

## Step 10: Push the Docker Image to ECR

After authentication and tagging, I pushed the image to the private ECR repository.

```bash
docker push \
  <aws-account-id>.dkr.ecr.us-east-1.amazonaws.com/<repository-name>:latest
```

Docker uploaded the image layers to Amazon ECR.

After a successful push, Docker displayed upload progress and a digest for the published image.

The image was now stored in the private AWS repository.

---

## Step 11: Verify the Repository in the AWS Console

I returned to the AWS Management Console.

I navigated to:

```text
Amazon ECR
    |
    v
Private registry
    |
    v
Repositories
```

I confirmed that the required repository appeared in the repository list.

I opened the repository and checked its details.

I verified the following:

- The repository existed.
- The repository visibility was private.
- The repository was in `us-east-1`.
- The image was available in the repository.

---

## Step 12: Verify the Uploaded Image in the AWS Console

Inside the repository, I opened the **Images** section.

I checked the uploaded image's tag and digest.

The expected image entry was similar to:

```text
Image tag: latest
Image digest: sha256:<image-digest>
```

The actual tag depends on the tag used when pushing the image.

The presence of the expected image entry confirmed that the image had been uploaded to ECR.

---

## Step 13: Verify Using AWS CLI

I also verified the ECR repository using AWS CLI commands.

### Verify the Repository

```bash
aws ecr describe-repositories \
  --region us-east-1 \
  --repository-names <repository-name>
```

### Verify the Uploaded Image

```bash
aws ecr describe-images \
  --region us-east-1 \
  --repository-name <repository-name>
```

### Display Image Tags and Digests

```bash
aws ecr describe-images \
  --region us-east-1 \
  --repository-name <repository-name> \
  --query 'imageDetails[*].[imageTags,imageDigest,imagePushedAt]' \
  --output table
```

These commands retrieve the repository and image information from Amazon ECR without creating or modifying resources.

The commands are also documented in `commands.md`.

---

# Docker-to-ECR Workflow

The overall workflow for this challenge was:

```text
Dockerfile Provided by Lab
          |
          v
   Build Docker Image
          |
          v
  Verify Local Image
          |
          v
 Create Private ECR Repository
          |
          v
 Obtain Repository URI
          |
          v
 Authenticate Docker with ECR
          |
          v
   Tag Docker Image
          |
          v
    Push Image to ECR
          |
          v
 Verify Repository and Image
          |
          v
      Task Completed
```

---

# What I Learned

From this challenge, I learned and practiced:

- How to create a private Amazon ECR repository.
- How to navigate the Amazon ECR Console.
- How to build a Docker image from an existing Dockerfile.
- How to verify local Docker images.
- How to obtain an ECR repository URI.
- How Docker authenticates with a private ECR registry.
- How to tag a Docker image for a remote registry.
- How to push container images to Amazon ECR.
- How to verify repositories and image digests using AWS CLI.
- How Amazon ECR can be used to store images for containerized applications.
- Why repository permissions and credential security are important.

---

# Challenge Status

Day 28 — Completed Successfully ✅

**Created a private Amazon ECR repository, built a Docker image from the lab-provided Dockerfile, pushed the image to ECR, and verified the repository and image using the AWS Console and AWS CLI.**

100 Days. 100 Challenges. One Cloud Journey. ☁️🚀

I will continue documenting each challenge to build my practical AWS, Docker, and cloud infrastructure skills.
