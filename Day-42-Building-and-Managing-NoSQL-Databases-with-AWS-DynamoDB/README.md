
# Day 42 - Building and Managing NoSQL Databases with AWS DynamoDB

## Challenge Overview

This is Day 42 of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The challenge was related to **Amazon DynamoDB, NoSQL Database Management, Table Creation, and Data Insertion**.

The Nautilus DevOps team was developing a simple To-Do application using Amazon DynamoDB to store and manage tasks efficiently.

The application required a database table where each task could be uniquely identified using a task ID. Each task also needed a description and a status to indicate its current progress.

For this task, I was required to create a DynamoDB table, configure its primary key, insert two task records, and verify the stored data using the AWS Management Console.

### Challenge Requirement

The requirements were:

1. Access the AWS Management Console.
2. Navigate to Amazon DynamoDB.
3. Create a DynamoDB table named `datacenter-tasks`.
4. Configure `taskId` as the primary key.
5. Set the primary key data type to `String`.
6. Insert the first task with the following details:
   - taskId: `1`
   - description: `Learn DynamoDB`
   - status: `completed`
7. Insert the second task with the following details:
   - taskId: `2`
   - description: `Build To-Do App`
   - status: `in-progress`
8. Verify that both records were inserted successfully.
9. Confirm the status of each task.

---

## Objectives

The objectives of this challenge are:

- Understand Amazon DynamoDB.
- Learn the fundamentals of NoSQL databases.
- Create and configure a DynamoDB table.
- Understand partition keys and primary keys.
- Insert items into a DynamoDB table.
- Work with attributes and data types.
- Retrieve records from DynamoDB.
- Verify database records using the AWS Console.
- Understand the difference between SQL and NoSQL databases.

---

## Environment

| Item | Details |
| ----------------- | ------------------------------ |
| Challenge | 100 Days of Cloud (AWS) |
| Platform | KodeKloud |
| Day | 42 |
| Cloud Provider | Amazon Web Services (AWS) |
| Service | Amazon DynamoDB |
| Region | `us-east-1` |
| Table Name | `datacenter-tasks` |
| Partition Key | `taskId` |
| Key Data Type | `String` |
| Sort Key | Not Configured |
| Total Items | 2 |
| Status | Completed |

---

# Solution

## Step 1: Access the AWS Management Console

First, I accessed the AWS Management Console using the temporary credentials provided by the KodeKloud lab environment.

After logging in, I selected the required AWS region:

```text
us-east-1
```

All resources for this challenge were created in the specified region.

---

## Step 2: Open Amazon DynamoDB

From the AWS Management Console, I searched for **DynamoDB** in the AWS search bar.

I opened the DynamoDB service and navigated to the Tables section.

The navigation path was:

```text
AWS Management Console
        |
        v
    DynamoDB
        |
        v
      Tables
        |
        v
   Create Table
```

---

## Step 3: Create the DynamoDB Table

Inside the DynamoDB Tables section, I selected **Create table**.

I entered the following configuration:

```text
Table name: datacenter-tasks

Partition key: taskId

Partition key type: String

Sort key: Not configured
```

The partition key uniquely identifies each item in the table.

For this task, I used `taskId` as the partition key because every task needed a unique identifier.

---

## Step 4: Configure Table Settings

After entering the table name and partition key, I reviewed the additional table settings.

For this lab, I kept the default table settings because no additional configuration was required.

I then selected **Create table**.

DynamoDB started creating the table.

---

## Step 5: Verify the Table Creation

After the table was created, I returned to the DynamoDB Tables section.

I opened the `datacenter-tasks` table and checked its configuration.

The table contained the following configuration:

```text
Table Name: datacenter-tasks

Partition Key: taskId

Partition Key Type: String

Sort Key: None

Table Status: Active
```

This confirmed that the DynamoDB table was created successfully.

---

## Step 6: Create the First Task

After creating the table, I opened the `datacenter-tasks` table.

I navigated to the **Explore table items** section and selected **Create item**.

I entered the following attributes for the first task:

```text
taskId      : 1

description : Learn DynamoDB

status      : completed
```

All three attributes were configured with the String data type.

After entering the values, I selected **Create item** to save the first record.

---

## Step 7: Verify the First Task

After saving the first item, I checked the table items section.

The first task appeared with the following details:

| Attribute | Value | Data Type |
| --------- | ---------------- | --------- |
| taskId | `1` | String |
| description | Learn DynamoDB | String |
| status | completed | String |

This confirmed that the first task was inserted successfully.

---

## Step 8: Create the Second Task

Next, I selected **Create item** again to insert the second task.

I entered the following attributes:

```text
taskId      : 2

description : Build To-Do App

status      : in-progress
```

I kept all attributes as String values.

After entering the information, I selected **Create item**.

The second task was successfully saved in the DynamoDB table.

---

## Step 9: Verify the Second Task

After inserting the second item, I returned to the table items section.

I checked the second record:

| Attribute | Value | Data Type |
| --------- | ---------------- | --------- |
| taskId | `2` | String |
| description | Build To-Do App | String |
| status | in-progress | String |

This confirmed that the second task was inserted successfully.

---

## Step 10: Verify All Table Items

Finally, I opened the **Explore table items** section.

I verified that both task records were available in the `datacenter-tasks` table.

The final records were:

| taskId | description | status |
| ------ | ---------------- | ------------ |
| 1 | Learn DynamoDB | completed |
| 2 | Build To-Do App | in-progress |

Both records were stored with the correct attributes and values.

---

# Database Structure

The final DynamoDB table structure was:

```text
Table: datacenter-tasks

Primary Key:
    taskId (String)

Items:

Item 1:
{
    "taskId": "1",
    "description": "Learn DynamoDB",
    "status": "completed"
}

Item 2:
{
    "taskId": "2",
    "description": "Build To-Do App",
    "status": "in-progress"
}
```

---

# AWS Console Verification

I verified the following information through the AWS Management Console:

1. The `datacenter-tasks` table existed.
2. The table status was `Active`.
3. The partition key was `taskId`.
4. The partition key data type was `String`.
5. The first task was inserted successfully.
6. The second task was inserted successfully.
7. Task 1 had the status `completed`.
8. Task 2 had the status `in-progress`.

---

# AWS CLI Verification

Although the database was created and managed entirely through the AWS Management Console, I also used AWS CLI commands to verify the resources.

The verification commands are documented separately in:

```text
commands.md
```

These commands were used to:

- Verify the DynamoDB table.
- Check the table configuration.
- Retrieve the first task.
- Retrieve the second task.
- Verify the task descriptions and statuses.
- List all items stored in the table.

---

# What I Learned

From this challenge, I learned and practiced:

- How to access Amazon DynamoDB through the AWS Console.
- How to create a NoSQL database table.
- How DynamoDB tables store data.
- What a partition key is and why it is important.
- How to configure a String partition key.
- How to create and insert items into DynamoDB.
- How to store multiple attributes in a single item.
- How to retrieve specific records using their primary keys.
- How to verify stored data through the AWS Console.
- How to verify DynamoDB resources using AWS CLI.
- The basic differences between relational and NoSQL databases.

---

# Challenge Status

Day 42 — Completed Successfully ✅

**Created the `datacenter-tasks` DynamoDB table, configured `taskId` as the String partition key, inserted both task records, and verified that Task 1 had the `completed` status and Task 2 had the `in-progress` status using the AWS Management Console.**

100 Days. 100 Challenges. One Cloud Journey. ☁️🚀

I will continue documenting each challenge and building my practical AWS cloud skills step by step.
