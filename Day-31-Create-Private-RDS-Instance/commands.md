# Day 31 - AWS RDS Verification Commands

This file contains the AWS CLI verification commands used for **Day 31** of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The task was to create a private Amazon RDS instance named `datacenter-rds` using MySQL 8.4.x in the `us-east-1` region.

All commands in this file are for **verification purposes only**.

---

# 1. Verify AWS CLI Installation

Check whether AWS CLI is installed:

```bash
aws --version
```

---

# 2. Verify AWS Identity

Check the AWS account and identity associated with the configured credentials:

```bash
aws sts get-caller-identity
```

---

# 3. Verify RDS Instance

Check whether the required RDS instance exists:

```bash
aws rds describe-db-instances \
    --db-instance-identifier datacenter-rds \
    --region us-east-1
```

This command displays the complete configuration of the specified RDS instance.

---

# 4. Verify Database Status

Check the current status of the RDS instance:

```bash
aws rds describe-db-instances \
    --db-instance-identifier datacenter-rds \
    --region us-east-1 \
    --query 'DBInstances[0].DBInstanceStatus' \
    --output text
```

Expected output:

```text
available
```

---

# 5. Verify Database Engine and Version

Check the database engine and its version:

```bash
aws rds describe-db-instances \
    --db-instance-identifier datacenter-rds \
    --region us-east-1 \
    --query 'DBInstances[0].[Engine,EngineVersion]' \
    --output table
```

Expected information:

```text
Engine:        mysql
EngineVersion: 8.4.x
```

The exact patch version may vary within the required 8.4 release series.

---

# 6. Verify DB Instance Class

Check the configured RDS instance class:

```bash
aws rds describe-db-instances \
    --db-instance-identifier datacenter-rds \
    --region us-east-1 \
    --query 'DBInstances[0].DBInstanceClass' \
    --output text
```

Expected output:

```text
db.t3.micro
```

---

# 7. Verify Storage Configuration

Check the allocated storage and storage type:

```bash
aws rds describe-db-instances \
    --db-instance-identifier datacenter-rds \
    --region us-east-1 \
    --query 'DBInstances[0].[StorageType,AllocatedStorage]' \
    --output table
```

Expected information:

```text
Storage Type:      gp2
Allocated Storage: 20
```

---

# 8. Verify Storage Autoscaling

Check whether storage autoscaling is enabled and verify the maximum allocated storage:

```bash
aws rds describe-db-instances \
    --db-instance-identifier datacenter-rds \
    --region us-east-1 \
    --query 'DBInstances[0].[MaxAllocatedStorage]' \
    --output table
```

Expected output:

```text
22
```

A positive `MaxAllocatedStorage` value indicates that storage autoscaling is configured.

---

# 9. Verify Public Accessibility

Check whether the RDS instance is publicly accessible:

```bash
aws rds describe-db-instances \
    --db-instance-identifier datacenter-rds \
    --region us-east-1 \
    --query 'DBInstances[0].PubliclyAccessible' \
    --output text
```

Expected output:

```text
False
```

This confirms that public accessibility is disabled.

---

# 10. Verify Complete RDS Configuration

To display the important configuration details in one table:

```bash
aws rds describe-db-instances \
    --db-instance-identifier datacenter-rds \
    --region us-east-1 \
    --query 'DBInstances[0].[DBInstanceIdentifier,DBInstanceStatus,Engine,EngineVersion,DBInstanceClass,StorageType,AllocatedStorage,MaxAllocatedStorage,PubliclyAccessible]' \
    --output table
```

The expected configuration is:

```text
DB Identifier:       datacenter-rds
Status:              available
Engine:              mysql
Engine Version:      8.4.x
Instance Class:      db.t3.micro
Storage Type:        gp2
Allocated Storage:   20 GiB
Maximum Storage:     22 GiB
Publicly Accessible: False
```

---

# 11. Verify RDS Instance Endpoint

Check the database endpoint and port:

```bash
aws rds describe-db-instances \
    --db-instance-identifier datacenter-rds \
    --region us-east-1 \
    --query 'DBInstances[0].[Endpoint.Address,Endpoint.Port]' \
    --output table
```

This command displays the RDS endpoint and MySQL port.

The default MySQL port is:

```text
3306
```

---

# 12. Verify RDS Instance Availability and Configuration

Run the following command to display the essential configuration in a single readable output:

```bash
aws rds describe-db-instances \
    --db-instance-identifier datacenter-rds \
    --region us-east-1 \
    --query 'DBInstances[0].{Identifier:DBInstanceIdentifier,Status:DBInstanceStatus,Engine:Engine,Version:EngineVersion,Class:DBInstanceClass,StorageType:StorageType,AllocatedStorage:AllocatedStorage,MaxStorage:MaxAllocatedStorage,PublicAccess:PubliclyAccessible}' \
    --output json
```

This provides a consolidated verification of the database configuration.

---

# Day 31 Final Verification

```text
DB Identifier       : datacenter-rds
Engine              : MySQL
Engine Version      : 8.4.x
Instance Class      : db.t3.micro
Storage Type        : gp2
Allocated Storage   : 20 GiB
Storage Autoscaling : Enabled
Maximum Storage     : 22 GiB
Public Access       : No
AWS Region          : us-east-1
Status              : available
```

**Day 31 — Completed Successfully ✅**

All commands in this document are intended for AWS resource verification only.
