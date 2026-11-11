
# Day 42 - AWS DynamoDB Verification Commands

This file contains the AWS CLI verification commands used for **Day 42** of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The task was to create a DynamoDB table named `datacenter-tasks`, insert two task records, and verify their attributes and statuses.

All database creation and data insertion operations were performed through the AWS Management Console.

The following commands were used only for verification.

---

# 1. Verify AWS CLI Configuration

Check the AWS CLI version:

```bash
aws --version
```

---

# 2. Verify AWS Identity

Check the AWS account and identity currently being used:

```bash
aws sts get-caller-identity
```

---

# 3. Verify DynamoDB Table Exists

List all DynamoDB tables in the required region:

```bash
aws dynamodb list-tables \
    --region us-east-1
```

The output should contain:

```text
datacenter-tasks
```

---

# 4. Describe the DynamoDB Table

Check the table configuration and status:

```bash
aws dynamodb describe-table \
    --table-name datacenter-tasks \
    --region us-east-1
```

This command displays:

- Table name
- Table status
- Table ARN
- Primary key configuration
- Item count
- Table size
- Provisioned throughput information

---

# 5. Verify Table Name and Status

Display the table name and current status:

```bash
aws dynamodb describe-table \
    --table-name datacenter-tasks \
    --region us-east-1 \
    --query 'Table.[TableName,TableStatus]' \
    --output table
```

Expected information:

```text
datacenter-tasks    ACTIVE
```

---

# 6. Verify Primary Key Configuration

Check the partition key name and data type:

```bash
aws dynamodb describe-table \
    --table-name datacenter-tasks \
    --region us-east-1 \
    --query 'Table.KeySchema'
```

Expected information:

```json
[
    {
        "AttributeName": "taskId",
        "KeyType": "HASH"
    }
]
```

Check the partition key data type:

```bash
aws dynamodb describe-table \
    --table-name datacenter-tasks \
    --region us-east-1 \
    --query 'Table.AttributeDefinitions'
```

Expected information:

```json
[
    {
        "AttributeName": "taskId",
        "AttributeType": "S"
    }
]
```

Here:

- `HASH` represents the partition key.
- `S` represents the String data type.

---

# 7. Verify the First Task

Retrieve Task 1 using its primary key:

```bash
aws dynamodb get-item \
    --table-name datacenter-tasks \
    --key '{"taskId":{"S":"1"}}' \
    --region us-east-1 \
    --consistent-read
```

Expected information:

```json
{
    "Item": {
        "taskId": {
            "S": "1"
        },
        "description": {
            "S": "Learn DynamoDB"
        },
        "status": {
            "S": "completed"
        }
    }
}
```

This confirms that Task 1 exists and has the correct status.

---

# 8. Verify the Second Task

Retrieve Task 2 using its primary key:

```bash
aws dynamodb get-item \
    --table-name datacenter-tasks \
    --key '{"taskId":{"S":"2"}}' \
    --region us-east-1 \
    --consistent-read
```

Expected information:

```json
{
    "Item": {
        "taskId": {
            "S": "2"
        },
        "description": {
            "S": "Build To-Do App"
        },
        "status": {
            "S": "in-progress"
        }
    }
}
```

This confirms that Task 2 exists and has the correct status.

---

# 9. Retrieve All Items from the Table

To retrieve all records stored in the DynamoDB table:

```bash
aws dynamodb scan \
    --table-name datacenter-tasks \
    --region us-east-1
```

This command retrieves all items from the table.

Expected records:

```text
Task 1:
taskId      = 1
description = Learn DynamoDB
status      = completed

Task 2:
taskId      = 2
description = Build To-Do App
status      = in-progress
```

---

# 10. Verify Only the Required Attributes

To display the task IDs, descriptions, and statuses in a readable table:

```bash
aws dynamodb scan \
    --table-name datacenter-tasks \
    --region us-east-1 \
    --projection-expression "taskId, description, #s" \
    --expression-attribute-names '{"#s":"status"}' \
    --output table
```

The expression attribute name `#s` is used as an alias for the `status` attribute.

Expected information:

```text
-----------------------------------------------
|                   Scan                      |
+----------+------------------+----------------+
| taskId   | description      | status         |
+----------+------------------+----------------+
| 1        | Learn DynamoDB   | completed      |
| 2        | Build To-Do App  | in-progress    |
+----------+------------------+----------------+
```

---

# Final Verification

The final database configuration was:

```text
Table Name       : datacenter-tasks
Partition Key    : taskId
Key Data Type    : String
Table Status     : ACTIVE
Total Items      : 2

Task 1:
Description      : Learn DynamoDB
Status           : completed

Task 2:
Description      : Build To-Do App
Status           : in-progress
```

---

# Day 42 Result

```text
Amazon DynamoDB
       |
       v
datacenter-tasks
       |
       +-- Partition Key: taskId (String)
       |
       +-- Task 1
       |      |
       |      +-- Learn DynamoDB
       |      +-- completed
       |
       +-- Task 2
              |
              +-- Build To-Do App
              +-- in-progress

Verification: Successful
```

**Day 42 — Completed Successfully ✅**
