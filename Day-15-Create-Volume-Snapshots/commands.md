# Day 15 - EBS Snapshot Verification Commands

This file contains **only the AWS CLI verification commands** used for **Day 15** of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The EBS volume snapshot was created using the **AWS Management Console**.

The commands below were used only to verify the created snapshot.

---

# 1. List EBS Snapshots

To list snapshots in the required AWS region:

```bash
aws ec2 describe-snapshots \
    --region us-east-1
```

---

# 2. List Snapshot IDs and States

To verify the snapshot ID and current state:

```bash
aws ec2 describe-snapshots \
    --region us-east-1 \
    --query 'Snapshots[*].[SnapshotId,State]' \
    --output table
```

The snapshot should eventually show:

```text
completed
```

---

# 3. Verify Snapshot Details

To display important snapshot information:

```bash
aws ec2 describe-snapshots \
    --region us-east-1 \
    --query 'Snapshots[*].[SnapshotId,VolumeId,State,VolumeSize,StartTime]' \
    --output table
```

This verifies:

```text
Snapshot ID
Volume ID
Snapshot State
Volume Size
Start Time
```

---

# 4. Verify Completed Snapshots

To display only snapshots whose state is `completed`:

```bash
aws ec2 describe-snapshots \
    --region us-east-1 \
    --filters Name=status,Values=completed \
    --query 'Snapshots[*].[SnapshotId,VolumeId,State,VolumeSize]' \
    --output table
```

---

# 5. Verify Snapshot by Snapshot ID

If the snapshot ID is known:

```bash
aws ec2 describe-snapshots \
    --snapshot-ids <snapshot-id> \
    --region us-east-1
```

Replace:

```text
<snapshot-id>
```

with the actual snapshot ID.

---

# 6. Verify Snapshot Source Volume

To verify which EBS volume the snapshot was created from:

```bash
aws ec2 describe-snapshots \
    --region us-east-1 \
    --query 'Snapshots[*].[SnapshotId,VolumeId]' \
    --output table
```

---

Expected state after successful completion:

```text
completed
```

---

# 7. Verify Snapshots Created in the Region

To verify snapshots available in `us-east-1`:

```bash
aws ec2 describe-snapshots \
    --region us-east-1 \
    --query 'Snapshots[*].[SnapshotId,State,StartTime]' \
    --output table
```

---

# Final Verification

The important verification result was:

```text
Snapshot State : completed
Region         : us-east-1
```

**Day 15 — EBS Volume Snapshot Successfully Verified**
