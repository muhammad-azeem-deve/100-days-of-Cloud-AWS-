# Day 15 - Create Volume Snapshots

## Challenge Overview

This is Day 15 of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The challenge was related to **Amazon EBS Volume Snapshots**.

Amazon EBS snapshots are point-in-time backups of EBS volumes. They can be used to preserve data and create backups that can later be used to restore or create new EBS volumes.

The task was to create a snapshot of the required EBS volume using the **AWS Management Console** and verify that the snapshot was created successfully.

### Challenge Requirement

The requirements were:

1. Access the AWS Management Console.
2. Use the **us-east-1** AWS region.
3. Navigate to the Amazon EC2 service.
4. Locate the required EBS volume.
5. Create a snapshot of the volume.
6. Verify the snapshot using the AWS Management Console.
7. Verify the snapshot using AWS CLI commands.

---

## Objectives

The objectives of this challenge are:

* Understand Amazon EBS volumes.
* Understand EBS snapshots.
* Create an EBS volume snapshot.
* Work with AWS EC2 storage resources.
* Verify snapshot status using the AWS Console.
* Verify AWS resources using AWS CLI.
* Understand how snapshots can be used for backup and recovery.

---

## Environment

| Item            | Details                   |
| --------------- | ------------------------- |
| Challenge       | 100 Days of Cloud (AWS)   |
| Platform        | KodeKloud                 |
| Day             | 15                        |
| Cloud Provider  | Amazon Web Services (AWS) |
| Service         | Amazon EC2 / EBS          |
| Resource        | EBS Volume Snapshot       |
| Region          | `us-east-1`               |
| Creation Method | AWS Management Console    |
| Verification    | AWS Console + AWS CLI     |
| Status          | Completed                 |

---

# Solution

## Step 1: Access the AWS Management Console

First, I logged into the AWS Management Console using the temporary credentials provided by the KodeKloud lab environment.

I made sure that the AWS region was set to:

```text
us-east-1
```

---

## Step 2: Open Amazon EC2

From the AWS Management Console, I opened the **EC2** service.

The navigation path was:

```text
AWS Management Console
        |
        v
      EC2
        |
        v
     Volumes
```

---

## Step 3: Locate the EBS Volume

Inside the EC2 dashboard, I opened the **Volumes** section under **Elastic Block Store**.

I located the EBS volume that was required by the KodeKloud task.

Before creating the snapshot, I verified the volume details such as:

```text
Volume ID
Volume Type
Size
Availability Zone
State
```

---

## Step 4: Create the Volume Snapshot

After selecting the required EBS volume, I used the snapshot creation option:

```text
EBS Volume
    |
    v
Actions
    |
    v
Create snapshot
```

I provided the required snapshot information and created the snapshot.

The snapshot was created from the selected EBS volume.

---

## Step 5: Open the Snapshots Section

After creating the snapshot, I navigated to:

```text
EC2
  -> Elastic Block Store
  -> Snapshots
```

The newly created snapshot appeared in the list.

---

## Step 6: Verify Snapshot Status

I selected the newly created snapshot and checked its status.

The snapshot initially required time to complete.

The important status was:

```text
Completed
```

Once the snapshot status became **Completed**, the snapshot was successfully created.

---

## Step 7: Verify Snapshot Details

I checked the snapshot details in the AWS Console.

The snapshot contained information such as:

```text
Snapshot ID
Volume ID
State
Volume Size
Start Time
Description
```

I confirmed that the snapshot was associated with the correct EBS volume.

---

# AWS Console Verification

The snapshot was successfully visible under:

```text
EC2
  -> Elastic Block Store
  -> Snapshots
```

The final snapshot state was:

```text
Completed
```

This confirmed that the EBS volume snapshot had been created successfully.

---

# AWS CLI Verification

After completing the task through the AWS Management Console, I used the AWS CLI only for verification.

The AWS CLI verification commands are documented separately in:

```text
commands.md
```

These commands were used to:

* List EBS snapshots.
* Check the snapshot state.
* Verify the snapshot ID.
* Verify the source EBS volume.
* Verify the snapshot size.
* Confirm that the snapshot was created in the correct region.

---

# EBS Snapshot Workflow

The overall workflow for this challenge was:

```text
AWS Management Console
        |
        v
      EC2
        |
        v
     Volumes
        |
        v
 Select EBS Volume
        |
        v
  Create Snapshot
        |
        v
    Snapshots
        |
        v
 Verify Snapshot
        |
        v
    Completed
```

---

# What I Learned

From this challenge, I learned and practiced:

* What Amazon EBS volumes are.
* What EBS snapshots are.
* How to create an EBS volume snapshot.
* How snapshots provide point-in-time backups of EBS volumes.
* How to locate snapshots in the EC2 console.
* How to check snapshot status.
* How to verify the source volume associated with a snapshot.
* How to use AWS CLI for resource verification.
* The importance of using the correct AWS region.

---

# Challenge Status

Day 15 — Completed Successfully 

**Created an EBS volume snapshot using the AWS Management Console and verified the snapshot successfully using both the AWS Console and AWS CLI.**

100 Days. 100 Challenges. One Cloud Journey. 

I will continue documenting each challenge and building my practical AWS cloud skills step by step.
