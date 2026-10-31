# Day 31 - Create a Private AWS RDS Instance

## Challenge Overview

This is Day 31 of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The challenge was related to **Amazon RDS, MySQL Database Configuration, Storage Management, and Database Security**.

The Nautilus Development Team needed a private relational database instance for development and testing purposes. The team decided to use Amazon RDS with the AWS Free Tier configuration to simplify database management and reduce infrastructure costs.

The task was to create a private MySQL RDS instance with specific compute, storage, and autoscaling requirements.

### Challenge Requirement

The requirements were:

1. Access the AWS Management Console using the provided KodeKloud lab credentials.
2. Use the `us-east-1` AWS region.
3. Create an RDS database using the **Full configuration** method.
4. Select the **Free tier** template.
5. Set the DB instance identifier to `datacenter-rds`.
6. Select MySQL as the database engine.
7. Use MySQL version `8.4.x`.
8. Select the DB instance class `db.t3.micro`.
9. Configure General Purpose SSD (`gp2`) storage.
10. Set allocated storage to `20 GiB`.
11. Enable storage autoscaling.
12. Set the maximum storage threshold to `22 GiB`.
13. Disable public access to keep the database private.
14. Wait until the RDS instance reaches the `available` state.

---

## Objectives

The objectives of this challenge are:

- Understand Amazon RDS.
- Learn how to create a managed relational database.
- Configure a MySQL database engine.
- Understand AWS RDS instance classes.
- Configure General Purpose SSD storage.
- Enable storage autoscaling.
- Understand private database connectivity.
- Learn how to verify RDS resources using the AWS Console and CLI.
- Understand the importance of database security.

---

## Environment

| Item | Details |
| --------------------- | ------------------------------ |
| Challenge | 100 Days of Cloud (AWS) |
| Platform | KodeKloud |
| Day | 31 |
| Cloud Provider | Amazon Web Services (AWS) |
| AWS Service | Amazon RDS |
| Database Engine | MySQL |
| Engine Version | 8.4.x |
| Configuration Method | Full configuration |
| Template | Free tier |
| DB Identifier | `datacenter-rds` |
| Instance Class | `db.t3.micro` |
| Storage Type | General Purpose SSD (gp2) |
| Allocated Storage | 20 GiB |
| Storage Autoscaling | Enabled |
| Maximum Storage | 22 GiB |
| Public Access | No |
| AWS Region | `us-east-1` |
| Final Status | Available |

---

# Solution

## Step 1: Access the AWS Management Console

First, I accessed the AWS Management Console using the temporary credentials provided by the KodeKloud lab environment.

I made sure to select the required AWS region:

```text
us-east-1
```

All resources for this challenge had to be created in this region.

---

## Step 2: Open Amazon RDS

From the AWS Management Console, I searched for **RDS** and opened the Amazon RDS service.

The navigation path was:

```text
AWS Management Console
        |
        v
    Amazon RDS
        |
        v
   Databases
        |
        v
  Create database
```

---

## Step 3: Select Database Creation Method

On the Create database page, I selected:

```text
Database creation method: Full configuration
```

I selected Full configuration because it allows detailed control over the database engine, instance class, storage, connectivity, and other settings.

---

## Step 4: Select Database Engine

I selected MySQL as the database engine.

The configuration was:

```text
Engine type: MySQL
Engine version: 8.4.x
```

This ensured that the database used the required MySQL engine version.

---

## Step 5: Select the Free Tier Template

Under Templates, I selected:

```text
Free tier
```

This template provides a predefined configuration suitable for eligible free-tier workloads.

---

## Step 6: Configure Database Identifier

Under Settings, I configured the DB instance identifier:

```text
DB instance identifier: datacenter-rds
```

I also configured a master username and master password as required by the RDS setup.

The password was kept private and was not included in the GitHub documentation.

---

## Step 7: Configure Instance Class

Under the DB instance configuration section, I selected:

```text
DB instance class: db.t3.micro
```

This was the required instance class for the challenge.

---

## Step 8: Configure Storage

Under Storage configuration, I selected the following settings:

```text
Storage type: General Purpose SSD (gp2)
Allocated storage: 20 GiB
```

This configured the database with the required storage type and initial storage capacity.

---

## Step 9: Enable Storage Autoscaling

I enabled the storage autoscaling option.

The maximum storage threshold was configured as:

```text
Enable storage autoscaling: Yes
Maximum storage threshold: 22 GiB
```

Storage autoscaling allows Amazon RDS to automatically increase allocated storage when required, up to the configured maximum threshold.

---

## Step 10: Configure Database Connectivity

Under Connectivity, I configured the public access option:

```text
Public access: No
```

This ensured that the database was configured as a private RDS instance rather than being assigned public accessibility.

I left the other connectivity settings at their defaults as required by the lab.

---

## Step 11: Review Database Configuration

Before creating the database, I reviewed the selected configuration.

The final configuration was:

```text
DB Identifier:       datacenter-rds
Engine:              MySQL
Engine Version:      8.4.x
Template:            Free tier
Instance Class:      db.t3.micro
Storage Type:        gp2
Allocated Storage:   20 GiB
Storage Autoscaling: Enabled
Maximum Storage:     22 GiB
Public Access:       No
Region:              us-east-1
```

After reviewing the configuration, I selected **Create database**.

---

## Step 12: Wait for Database Creation

After selecting Create database, AWS started provisioning the RDS instance.

Initially, the database status was:

```text
Creating
```

I waited for AWS to complete the database provisioning process.

---

## Step 13: Verify RDS Instance Status

After the provisioning process was completed, I opened:

```text
Amazon RDS
    -> Databases
```

I located the database named:

```text
datacenter-rds
```

I verified that its status changed to:

```text
Available
```

This confirmed that the RDS instance had been successfully created.

---

## Step 14: Verify Database Configuration

I selected `datacenter-rds` from the RDS database list and reviewed its configuration.

I verified the following details:

| Configuration | Expected Value |
| --------------------- | ---------------- |
| DB Identifier | `datacenter-rds` |
| Engine | MySQL |
| Engine Version | 8.4.x |
| Instance Class | `db.t3.micro` |
| Storage Type | gp2 |
| Allocated Storage | 20 GiB |
| Storage Autoscaling | Enabled |
| Maximum Storage | 22 GiB |
| Public Access | No |
| Status | Available |
| Region | `us-east-1` |

---

# AWS CLI Verification

Although the database was created through the AWS Management Console, I also used AWS CLI commands to verify the RDS configuration.

The verification commands are documented separately in:

```text
commands.md
```

The CLI verification included:

- Checking the AWS identity.
- Describing the RDS database instance.
- Verifying the database engine and version.
- Checking the instance class.
- Verifying allocated storage and storage type.
- Checking storage autoscaling configuration.
- Verifying public accessibility.
- Checking the database status.

---

# RDS Creation Workflow

The overall workflow for this challenge was:

```text
AWS Management Console
        |
        v
    Amazon RDS
        |
        v
  Create Database
        |
        v
 Full Configuration
        |
        v
  Free Tier Template
        |
        v
   MySQL 8.4.x
        |
        v
  datacenter-rds
        |
        v
   db.t3.micro
        |
        v
  gp2 - 20 GiB
        |
        v
 Storage Autoscaling
   Maximum 22 GiB
        |
        v
  Public Access: No
        |
        v
  Create Database
        |
        v
   Status: Available
```

---

# What I Learned

From this challenge, I learned and practiced:

- How to navigate the Amazon RDS service.
- How to create an RDS database using Full configuration.
- How to select the Free tier database template.
- How to configure a MySQL database engine.
- How to select a specific MySQL engine version.
- How to configure an RDS instance class.
- How to configure General Purpose SSD storage.
- How storage autoscaling works in Amazon RDS.
- How to configure a maximum storage threshold.
- How to disable public accessibility for a database.
- How to verify database configuration through the AWS Console.
- How to inspect RDS resources using AWS CLI.
- Why private database access is important for security.

# Challenge Status

Day 31 — Completed Successfully ✅

**Created the private AWS RDS instance named `datacenter-rds` using MySQL 8.4.x, configured the `db.t3.micro` instance class, allocated 20 GiB of gp2 storage, enabled storage autoscaling with a maximum threshold of 22 GiB, disabled public access, and verified that the database reached the `available` state in the `us-east-1` region.**

100 Days. 100 Challenges. One Cloud Journey. ☁️🚀

I will continue documenting each challenge and building my practical AWS cloud skills step by step.
