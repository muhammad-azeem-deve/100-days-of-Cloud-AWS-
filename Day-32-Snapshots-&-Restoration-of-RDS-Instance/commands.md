
# Day 32 - RDS Snapshot and Restoration Verification Commands

This file contains only AWS CLI verification commands for **Day 32** of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The challenge involved creating a manual snapshot of an existing RDS instance and restoring it into a new RDS instance.

All resources were created and configured using the AWS Management Console.

The AWS CLI was used only to verify the created resources.

---

# 1. Verify AWS CLI Version

```bash
aws --version
```

---

# 2. Verify AWS Account Identity

To check the currently authenticated AWS account:

```bash
aws sts get-caller-identity
```

---

# 3. Verify the Source RDS Instance

To verify the original database instance:

```bash
aws rds describe-db-instances \
    --db-instance-identifier devops-rds \
    --region us-east-1
```

To display only the important information:

```bash
aws rds describe-db-instances \
    --db-instance-identifier devops-rds \
    --region us-east-1 \
    --query 'DBInstances[0].[DBInstanceIdentifier,DBInstanceStatus,DBInstanceClass,Engine]' \
    --output table
```

Expected information:

```text
devops-rds
Available
```

---

# 4. Verify the Created DB Snapshot

To check whether the manual snapshot exists:

```bash
aws rds describe-db-snapshots \
    --db-snapshot-identifier devops-snapshot \
    --region us-east-1
```

To display snapshot details:

```bash
aws rds describe-db-snapshots \
    --db-snapshot-identifier devops-snapshot \
    --region us-east-1 \
    --query 'DBSnapshots[0].[DBSnapshotIdentifier,DBInstanceIdentifier,Status,SnapshotType,Engine]' \
    --output table
```

Expected information:

```text
Snapshot Identifier : devops-snapshot
Source DB Instance  : devops-rds
Status              : available
Snapshot Type       : manual
```

---

# 5. List All Manual DB Snapshots

To list manual snapshots in the required region:

```bash
aws rds describe-db-snapshots \
    --snapshot-type manual \
    --region us-east-1 \
    --query 'DBSnapshots[*].[DBSnapshotIdentifier,DBInstanceIdentifier,Status]' \
    --output table
```

The output should include:

```text
devops-snapshot
```

---

# 6. Verify the Restored RDS Instance

To verify that the restored database instance exists:

```bash
aws rds describe-db-instances \
    --db-instance-identifier devops-snapshot-restore \
    --region us-east-1
```

---

# 7. Verify Restored Instance Class and Status

To display the restored database configuration:

```bash
aws rds describe-db-instances \
    --db-instance-identifier devops-snapshot-restore \
    --region us-east-1 \
    --query 'DBInstances[0].[DBInstanceIdentifier,DBInstanceStatus,DBInstanceClass,Engine]' \
    --output table
```

Expected information:

```text
DB Instance Identifier : devops-snapshot-restore
DB Instance Status     : available
DB Instance Class      : db.t3.micro
```

---

# 8. Verify the Restored Database Endpoint

To check the endpoint of the restored database:

```bash
aws rds describe-db-instances \
    --db-instance-identifier devops-snapshot-restore \
    --region us-east-1 \
    --query 'DBInstances[0].Endpoint.Address' \
    --output text
```

This displays the endpoint assigned to the restored RDS instance.

---

# 9. Verify All RDS Instances

To list all RDS instances in the required region:

```bash
aws rds describe-db-instances \
    --region us-east-1 \
    --query 'DBInstances[*].[DBInstanceIdentifier,DBInstanceStatus,DBInstanceClass]' \
    --output table
```

The output should include both:

```text
devops-rds
devops-snapshot-restore
```

---

# 10. Final Verification

To verify the restored instance status:

```bash
aws rds describe-db-instances \
    --db-instance-identifier devops-snapshot-restore \
    --region us-east-1 \
    --query 'DBInstances[0].DBInstanceStatus' \
    --output text
```

Expected output:

```text
available
```

To verify the snapshot status:

```bash
aws rds describe-db-snapshots \
    --db-snapshot-identifier devops-snapshot \
    --region us-east-1 \
    --query 'DBSnapshots[0].Status' \
    --output text
```

Expected output:

```text
available
```

---

# Final Result

```text
Source DB Instance : devops-rds
Source Status      : available

Snapshot Name      : devops-snapshot
Snapshot Type      : manual
Snapshot Status    : available

Restored Instance  : devops-snapshot-restore
Instance Class     : db.t3.micro
Restored Status    : available

AWS Region         : us-east-1
Final Status       : Successfully Verified
```

**Day 32 — Completed Successfully ✅**

100 Days. 100 Challenges. One Cloud Journey. ☁️🚀
