# Day 23 - Migration of Data Between S3 Buckets Using AWS CLI

## Challenge Overview

This is Day 23 of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The challenge was related to **Amazon S3, Data Migration, and AWS CLI Verification**.

The task involved migrating data from one Amazon S3 bucket to another. This is a common cloud operation when transferring files between environments, reorganizing storage, or moving application data to a different bucket.

I used the **AWS Management Console** to perform the migration and verify the migrated objects. I also prepared AWS CLI commands to independently verify the source and destination buckets.

### Challenge Requirement

The requirements were:

1. Access the AWS Management Console using the KodeKloud lab environment.
2. Select the required AWS region.
3. Open the source S3 bucket and inspect its contents.
4. Identify the objects that need to be migrated.
5. Copy the required objects to the destination S3 bucket.
6. Verify that the migrated objects exist in the destination bucket.
7. Use AWS CLI commands to verify the source and destination data.

---

## Objectives

The objectives of this challenge are:

- Understand Amazon S3 buckets and objects.
- Learn how to migrate data between S3 buckets.
- Use the AWS Management Console for object migration.
- Verify migrated files through the AWS Console.
- Learn the difference between copying and moving objects.
- Verify bucket contents using AWS CLI commands.
- Understand the importance of checking the destination after migration.

---

## Environment

| Item | Details |
|---|---|
| Challenge | 100 Days of Cloud (AWS) |
| Platform | KodeKloud |
| Day | 23 |
| Cloud Provider | Amazon Web Services (AWS) |
| Service | Amazon S3 |
| Source | Source S3 bucket provided by the lab |
| Destination | Destination S3 bucket provided by the lab |
| Migration Method | AWS Management Console |
| Verification | AWS Console and AWS CLI |
| Status | Completed after successful verification |

---

# Solution

## Step 1: Access the AWS Management Console

First, I logged in to the AWS Management Console using the temporary credentials provided by the KodeKloud lab.

I selected the AWS region specified by the challenge.

All resources were accessed in the required region.

---

## Step 2: Open Amazon S3

From the AWS Management Console, I opened the Amazon S3 service.

The navigation path was:

```text
AWS Management Console
        |
        v
   Amazon S3
        |
        v
     Buckets
```

The S3 Buckets page displayed the available buckets in the lab environment.

---

## Step 3: Identify the Source Bucket

I opened the source bucket specified by the challenge.

I inspected its contents and identified the files that needed to be migrated.

Before copying the data, I checked the object names to ensure that the correct files would be transferred.

---

## Step 4: Identify the Destination Bucket

Next, I returned to the S3 Buckets page and opened the destination bucket specified by the lab.

I checked its existing contents to understand which objects were already present.

This helped me verify the migration accurately after copying the files.

---

## Step 5: Copy Data Between S3 Buckets

I returned to the source bucket and selected the objects that needed to be migrated.

I used the available S3 Console actions to copy the selected objects to the destination bucket.

The migration workflow was:

```text
Source S3 Bucket
       |
       v
 Select Objects
       |
       v
   Copy Objects
       |
       v
Destination S3 Bucket
```

I selected the correct destination bucket and completed the copy operation.

**Note:** Copying objects preserves the originals in the source bucket. I did not delete the source objects because the task was to migrate data by copying it unless the lab explicitly required their removal.

---

## Step 6: Verify the Destination Bucket

After the copy operation completed, I opened the destination bucket.

I checked the object list to confirm that the expected files appeared in the destination.

I verified the object names and, where available, their sizes and other displayed metadata.

The migration was considered successful only after the expected objects were visible in the destination bucket.

---

## Step 7: Verify the Source Bucket

I returned to the source bucket and checked its contents again.

Because the operation was a copy, the original objects should remain in the source bucket.

This confirmed that the data was copied rather than removed from its original location.

---

## Step 8: Verify Using AWS CLI

After performing the migration through the AWS Management Console, I used AWS CLI commands to verify the source and destination buckets.

The commands are documented separately in `commands.md`.

They are intended for verification only and do not perform the migration.

The verification included:

- Listing the source bucket objects.
- Listing the destination bucket objects.
- Checking the destination object metadata.
- Comparing the expected source and destination contents.

---

# Verification Checklist

- [ ] Source bucket identified.
- [ ] Required source objects inspected.
- [ ] Destination bucket identified.
- [ ] Required objects copied to the destination.
- [ ] Destination objects verified through the AWS Console.
- [ ] Source objects checked to ensure they remain available.
- [ ] AWS CLI verification commands executed successfully.

---

# What I Learned

From this challenge, I learned and practiced:

- How to navigate Amazon S3 in the AWS Management Console.
- How S3 buckets store objects.
- How to copy objects between S3 buckets.
- How to verify a copy operation through the Console.
- The difference between copying and deleting or moving objects.
- How to inspect bucket contents using the AWS CLI.
- How to verify object metadata.
- Why verifying both the source and destination is important during data migration.

# Challenge Status

**Day 23 — Completed Successfully**

Migrated the required data between S3 buckets using the AWS Management Console and prepared AWS CLI commands to verify the source and destination contents.

100 Days. 100 Challenges. One Cloud Journey.

I will continue documenting each challenge and building my practical AWS cloud skills step by step.
