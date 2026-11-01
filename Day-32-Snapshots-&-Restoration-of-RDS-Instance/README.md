
# Day 32 - Snapshot and Restoration of an RDS Instance

## Challenge Overview

This is Day 32 of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The challenge was related to **Amazon RDS Snapshots, Database Backup, Restoration, and Recovery**.

The Nautilus Development Team was preparing for a major update to its database infrastructure. To ensure data safety and a smooth transition, the team requested the DevOps team to take a snapshot of an existing RDS instance and restore it into a new database instance.

The snapshot would serve as a backup, while the restored database would be used for testing and validation purposes.

### Challenge Requirement

The requirements were:

1. Access the AWS Management Console.
2. Use the `us-east-1` AWS region.
3. Ensure that the existing RDS instance `devops-rds` is in the `Available` state.
4. Create a manual DB snapshot named `devops-snapshot`.
5. Wait until the snapshot status becomes `Available`.
6. Restore the snapshot into a new RDS instance named `devops-snapshot-restore`.
7. Configure the restored RDS instance with the `db.t3.micro` instance class.
8. Wait until the restored RDS instance reaches the `Available` state.

---

## Objectives

The objectives of this challenge are:

- Understand Amazon RDS.
- Learn how manual RDS snapshots work.
- Create a database backup using the AWS Console.
- Understand how to restore an RDS instance from a snapshot.
- Configure the DB instance class during restoration.
- Monitor snapshot and database restoration status.
- Verify restored resources using the AWS Console.
- Verify RDS resources using AWS CLI commands.
- Understand the importance of database backup and disaster recovery.

---

## Environment

| Item | Details |
| ----------------- | ------------------------------ |
| Challenge | 100 Days of Cloud (AWS) |
| Platform | KodeKloud |
| Day | 32 |
| Cloud Provider | Amazon Web Services (AWS) |
| Service | Amazon RDS |
| Region | `us-east-1` |
| Source DB Instance | `devops-rds` |
| Snapshot Name | `devops-snapshot` |
| Restored DB Instance | `devops-snapshot-restore` |
| DB Instance Class | `db.t3.micro` |
| Snapshot Type | Manual |
| Final DB Status | Available |
| Status | Completed |

---

# Solution

## Step 1: Access the AWS Management Console

First, I accessed the AWS Management Console using the temporary credentials provided by the KodeKloud lab environment.

After logging in, I selected the required AWS region:

```text
us-east-1
```

All resources for this challenge were managed in the specified region.

---

## Step 2: Open Amazon RDS

From the AWS Management Console, I searched for and opened the **Amazon RDS** service.

The navigation path was:

```text
AWS Management Console
        |
        v
    Amazon RDS
        |
        v
     Databases
```

---

## Step 3: Verify the Existing RDS Instance

Inside the RDS dashboard, I opened the **Databases** section.

I located the existing database instance:

```text
devops-rds
```

Before creating the snapshot, I checked its status.

The source database needed to be in the following state:

```text
DB Instance Identifier : devops-rds
Status                  : Available
```

If the instance was still modifying, backing up, or starting, I waited until it became `Available`.

---

## Step 4: Open the Snapshots Section

After confirming that the source database was available, I navigated to the Snapshots section.

The navigation path was:

```text
Amazon RDS
     |
     v
Snapshots
```

This section displays the available manual and automated DB snapshots.

---

## Step 5: Create a Manual DB Snapshot

Inside the Snapshots section, I selected:

```text
Take snapshot
```

I selected the source database and entered the required snapshot name.

The configuration was:

```text
DB Instance : devops-rds
Snapshot Name : devops-snapshot
```

After entering the details, I selected **Take snapshot**.

AWS started creating the manual snapshot of the existing RDS instance.

---

## Step 6: Verify Snapshot Creation

After initiating the snapshot, I returned to the Snapshots section.

I located:

```text
devops-snapshot
```

Initially, the snapshot could show the status:

```text
Creating
```

I waited until the snapshot status changed to:

```text
Available
```

This confirmed that the manual DB snapshot had been created successfully and was ready for restoration.

---

## Step 7: Select the Snapshot for Restoration

After the snapshot became available, I selected:

```text
devops-snapshot
```

Then I opened the snapshot's Actions menu and selected:

```text
Restore snapshot
```

AWS opened the database restoration configuration page.

---

## Step 8: Configure the Restored RDS Instance

On the restoration configuration page, I entered the new database instance identifier.

Under **Settings**, I configured:

```text
DB Instance Identifier:
devops-snapshot-restore
```

Under **Instance configuration**, I selected the required instance class.

The configuration was:

```text
Instance Class Category : Burstable classes
DB Instance Class       : db.t3.micro
```

I left the remaining configuration options at their default values as required by the lab.

---

## Step 9: Restore the DB Instance

After reviewing the configuration, I selected:

```text
Restore DB instance
```

AWS started creating a new RDS instance using the selected snapshot.

The new instance was:

```text
devops-snapshot-restore
```

The restoration process took some time to complete.

---

## Step 10: Monitor the Restoration Process

After starting the restoration, I returned to:

```text
Amazon RDS
     |
     v
  Databases
```

I located the restored database:

```text
devops-snapshot-restore
```

During restoration, its status could initially appear as:

```text
Creating
```

I waited until the database status changed to:

```text
Available
```

This confirmed that AWS had successfully restored the database instance from the manual snapshot.

---

## Step 11: Verify the Restored RDS Instance in AWS Console

After the restoration process completed, I selected:

```text
devops-snapshot-restore
```

I checked the database configuration and verified the following details:

```text
DB Instance Identifier : devops-snapshot-restore
DB Instance Class      : db.t3.micro
Status                 : Available
Region                 : us-east-1
```

I also verified that the original database instance and the manual snapshot were still present.

---

# Console Verification

The following resources were verified through the AWS Management Console.

### 1. Source RDS Instance

```text
DB Instance : devops-rds
Status      : Available
```

### 2. Manual DB Snapshot

```text
Snapshot Name : devops-snapshot
Snapshot Type : Manual
Status        : Available
```

### 3. Restored RDS Instance

```text
DB Instance : devops-snapshot-restore
Instance Class : db.t3.micro
Status        : Available
Region        : us-east-1
```

---

# RDS Snapshot and Restoration Workflow

The overall workflow for this challenge was:

```text
       Existing RDS Instance
             |
             v
          devops-rds
             |
             v
       Verify Available
             |
             v
       Create Snapshot
             |
             v
       devops-snapshot
             |
             v
       Verify Available
             |
             v
       Restore Snapshot
             |
             v
   Configure New DB Instance
             |
             v
   devops-snapshot-restore
             |
             v
       db.t3.micro
             |
             v
      Verify Available
             |
             v
       Task Completed
```

---

# What I Learned

From this challenge, I learned and practiced:

- How to navigate the Amazon RDS dashboard.
- How to check the status of an existing RDS instance.
- How to create manual database snapshots.
- How to monitor snapshot creation.
- How to restore an RDS instance from an existing snapshot.
- How to configure the DB instance class during restoration.
- How to monitor the database restoration process.
- How to verify restored resources using the AWS Console.
- How to verify RDS snapshots and instances using AWS CLI.
- Why database backups and snapshots are important for disaster recovery.
- How snapshots help protect databases before major infrastructure changes.

# Challenge Status

Day 32 — Completed Successfully ✅

**Created a manual snapshot named** **`devops-snapshot`** **from the existing RDS instance** **`devops-rds`**, **restored it as** **`devops-snapshot-restore`** **using the** **`db.t3.micro`** **instance class, and verified that the restored database reached the** **`Available`** **state.**

100 Days. 100 Challenges. One Cloud Journey. ☁️🚀

I will continue documenting each challenge and improving my practical AWS cloud skills step by step.
