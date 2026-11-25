# Day 50 - AWS EC2 and EBS Volume Expansion Commands

This file contains the AWS CLI and Linux commands used for **Day 50** of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The task was to increase the EBS volume attached to the EC2 instance `nautilus-ec2` from 8 GiB to 12 GiB and extend the root partition and filesystem.

---

# 1. Verify AWS CLI Installation

```bash
aws --version
```

---

# 2. Verify AWS Identity

```bash
aws sts get-caller-identity
```

This command verifies the AWS credentials currently being used.

---

# 3. Set AWS Region

The required region was `us-east-1`.

```bash
export AWS_DEFAULT_REGION=us-east-1
```

Verify:

```bash
echo $AWS_DEFAULT_REGION
```

---

# 4. Find the EC2 Instance

List EC2 instances:

```bash
aws ec2 describe-instances \
    --region us-east-1
```

Find the instance named `nautilus-ec2`:

```bash
aws ec2 describe-instances \
    --region us-east-1 \
    --filters "Name=tag:Name,Values=nautilus-ec2" \
    --query 'Reservations[*].Instances[*].[InstanceId,State.Name,PublicIpAddress]' \
    --output table
```

This command displays the instance ID, state, and public IP address.

---

# 5. Identify the Attached EBS Volume

After obtaining the instance ID, identify its attached EBS volumes:

```bash
aws ec2 describe-instances \
    --instance-ids <INSTANCE-ID> \
    --region us-east-1 \
    --query 'Reservations[*].Instances[*].BlockDeviceMappings[*].[DeviceName,Ebs.VolumeId]' \
    --output table
```

This command displays the device names and associated EBS volume IDs.

---

# 6. Check the Initial EBS Volume Size

Use the volume ID obtained in the previous step:

```bash
aws ec2 describe-volumes \
    --volume-ids <VOLUME-ID> \
    --region us-east-1 \
    --query 'Volumes[*].[VolumeId,Size,State]' \
    --output table
```

Initial volume size:

```text
8 GiB
```

---

# 7. Modify the EBS Volume

Increase the volume size to 12 GiB:

```bash
aws ec2 modify-volume \
    --volume-id <VOLUME-ID> \
    --size 12 \
    --region us-east-1
```

This command requests an increase in the EBS volume capacity.

---

# 8. Verify EBS Volume Modification

Check the volume modification status:

```bash
aws ec2 describe-volumes-modifications \
    --volume-ids <VOLUME-ID> \
    --region us-east-1
```

Display the modification details:

```bash
aws ec2 describe-volumes-modifications \
    --volume-ids <VOLUME-ID> \
    --region us-east-1 \
    --query 'VolumesModifications[*].[VolumeId,OriginalSize,TargetSize,ModificationState]' \
    --output table
```

The target volume size should be:

```text
12 GiB
```

---

# 9. Check the SSH Private Key

The private key was provided on the AWS client machine:

```bash
ls -l /root/nautilus-keypair.pem
```

Set the required permissions:

```bash
chmod 400 /root/nautilus-keypair.pem
```

---

# 10. Connect to the EC2 Instance

Use the public IP address obtained from the EC2 instance:

```bash
ssh -i /root/nautilus-keypair.pem <username>@<EC2-PUBLIC-IP>
```

After connecting, verify the hostname:

```bash
hostname
```

---

# 11. Check Block Devices

Inside the EC2 instance, list all block devices:

```bash
lsblk
```

Display filesystem information:

```bash
lsblk -f
```

These commands help identify the root disk, its partitions, and filesystem types.

---

# 12. Check Root Filesystem

Check the available root filesystem storage:

```bash
df -h /
```

Display filesystem type:

```bash
df -Th /
```

Identify the root filesystem device:

```bash
findmnt /
```

These commands help determine the correct partition and filesystem for expansion.

---

# 13. Extend the Root Partition

If the root filesystem is on a standard partition, install the required utility if it is not already available.

For Amazon Linux/RHEL-based systems:

```bash
sudo yum install -y cloud-utils-growpart
```

For Ubuntu/Debian-based systems:

```bash
sudo apt-get update
sudo apt-get install -y cloud-guest-utils
```

Check the disk and partition layout:

```bash
lsblk
```

For example, if the root partition is `/dev/nvme0n1p1` and the disk is `/dev/nvme0n1`:

```bash
sudo growpart /dev/nvme0n1 1
```

For a different disk layout, replace the device and partition number with the actual values identified earlier.

---

# 14. Extend the Filesystem

First, identify the filesystem type:

```bash
df -Th /
```

### For XFS Filesystem

```bash
sudo xfs_growfs /
```

### For ext4 Filesystem

```bash
sudo resize2fs <ROOT-PARTITION>
```

For example:

```bash
sudo resize2fs /dev/nvme0n1p1
```

Use only the command appropriate for the actual root filesystem.

---

# 15. Verify the Updated Disk Size

Check block devices:

```bash
lsblk
```

Check root filesystem capacity:

```bash
df -h /
```

Check filesystem type and capacity:

```bash
df -Th /
```

The EBS volume should now have a capacity of 12 GiB, and the root filesystem should reflect the expanded available storage.

---

# 16. Verify the EBS Volume Again

From the AWS client machine, verify the final EBS volume size:

```bash
aws ec2 describe-volumes \
    --volume-ids <VOLUME-ID> \
    --region us-east-1 \
    --query 'Volumes[*].[VolumeId,Size,State]' \
    --output table
```

Expected volume size:

```text
12 GiB
```

---

# Important AWS CLI Commands

### Find EC2 Instance

```bash
aws ec2 describe-instances \
    --filters "Name=tag:Name,Values=nautilus-ec2" \
    --region us-east-1
```

### Find Attached EBS Volume

```bash
aws ec2 describe-instances \
    --instance-ids <INSTANCE-ID> \
    --region us-east-1 \
    --query 'Reservations[*].Instances[*].BlockDeviceMappings[*].Ebs.VolumeId'
```

### Modify EBS Volume

```bash
aws ec2 modify-volume \
    --volume-id <VOLUME-ID> \
    --size 12 \
    --region us-east-1
```

### Check Volume Modification

```bash
aws ec2 describe-volumes-modifications \
    --volume-ids <VOLUME-ID> \
    --region us-east-1
```

---

# Important Linux Commands

| Command | Purpose |
|---|---|
| `lsblk` | Display block devices |
| `lsblk -f` | Display filesystem information |
| `df -h /` | Check root filesystem capacity |
| `df -Th /` | Check filesystem type and capacity |
| `findmnt /` | Identify the root filesystem device |
| `growpart` | Extend an existing partition |
| `xfs_growfs /` | Expand an XFS filesystem |
| `resize2fs` | Expand an ext2/ext3/ext4 filesystem |
| `chmod 400` | Restrict private key permissions |
| `ssh -i` | Connect using a private key |

---

# Day 50 - Final Result

```text
EC2 Instance       : nautilus-ec2
AWS Region         : us-east-1

Initial EBS Size   : 8 GiB
Updated EBS Size   : 12 GiB
Storage Increased  : 4 GiB

Root Mount Point   : /
Partition          : Extended
Filesystem         : Resized

SSH Access         : Successful
Storage Check      : Verified

Status             : Successfully Completed
```

**Day 50 — Completed Successfully ✅**

100 Days. 100 Challenges. One Cloud Journey. ☁️🚀
