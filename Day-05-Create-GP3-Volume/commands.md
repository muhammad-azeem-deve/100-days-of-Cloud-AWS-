# Day 05 - AWS EBS Volume Commands

This file contains the AWS CLI commands used for **Day 05** of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The task was to create an EBS volume with the following configuration:

```text
Name        : nautilus-volume
Volume Type : gp3
Volume Size : 2 GiB
Region      : us-east-1
```

---

# 1. Check AWS CLI

First, I checked whether the AWS CLI was available:

```bash
aws --version
```

---

# 2. Retrieve KodeKloud Lab Credentials

The KodeKloud AWS client provides the `showcreds` command to retrieve temporary AWS credentials.

```bash
showcreds
```

The credentials are temporary and should only be used for the lab environment.

> **Security Note:** Never add AWS access keys, secret keys, passwords, or session tokens to GitHub.

---

# 3. Configure AWS CLI

The AWS CLI can be configured using:

```bash
aws configure
```

The required AWS region was:

```text
us-east-1
```

Example:

```text
AWS Access Key ID: <temporary-access-key>
AWS Secret Access Key: <temporary-secret-key>
Default region name: us-east-1
Default output format: json
```

Actual credentials should never be stored in this repository.

---

# 4. Verify AWS Identity

I verified the AWS identity associated with the temporary lab credentials:

```bash
aws sts get-caller-identity
```

---

# 5. Create the EBS Volume

The required volume configuration was:

```text
Name        : nautilus-volume
Type        : gp3
Size        : 2 GiB
Region      : us-east-1
```

The AWS CLI command to create the volume was:

```bash
aws ec2 create-volume \
    --volume-type gp3 \
    --size 2 \
    --region us-east-1
```

This command creates a `2 GiB` EBS volume using the `gp3` volume type.

The command returns information about the newly created volume, including its:

```text
VolumeId
Size
State
VolumeType
AvailabilityZone
CreateTime
```

---

# 6. Add the Required Name Tag

The volume name is stored as a tag in AWS.

First, I obtained the volume ID from the output of the previous command.

Then I added the required Name tag:

```bash
aws ec2 create-tags \
    --resources <volume-id> \
    --tags Key=Name,Value=nautilus-volume \
    --region us-east-1
```

Replace:

```text
<volume-id>
```

with the actual volume ID returned by AWS.

For example:

```bash
aws ec2 create-tags \
    --resources vol-xxxxxxxxxxxxxxxxx \
    --tags Key=Name,Value=nautilus-volume \
    --region us-east-1
```

---

# 7. Verify All EBS Volumes

To list EBS volumes in the required region:

```bash
aws ec2 describe-volumes \
    --region us-east-1
```

---

# 8. Find the Nautilus Volume

To find the volume using its Name tag:

```bash
aws ec2 describe-volumes \
    --region us-east-1 \
    --filters "Name=tag:Name,Values=nautilus-volume"
```

---

# 9. Display Volume Information

The volume ID, size, type, and state can be displayed in table format:

```bash
aws ec2 describe-volumes \
    --region us-east-1 \
    --filters "Name=tag:Name,Values=nautilus-volume" \
    --query 'Volumes[*].[VolumeId,Size,VolumeType,State]' \
    --output table
```

Expected result:

```text
------------------------------------------------
|              DescribeVolumes                 |
+----------------+------+-------+--------------+
| Volume ID      | Size | Type  | State        |
+----------------+------+-------+--------------+
| vol-xxxxxxxx   | 2    | gp3   | available    |
+----------------+------+-------+--------------+
```

---

# 10. Verify the Volume Name

To verify the Name tag:

```bash
aws ec2 describe-volumes \
    --region us-east-1 \
    --filters "Name=tag:Name,Values=nautilus-volume" \
    --query 'Volumes[*].Tags' \
    --output table
```

The output should contain:

```text
Key     Value
Name    nautilus-volume
```

---

# 11. Verify Volume Type and Size

To display only the required configuration:

```bash
aws ec2 describe-volumes \
    --region us-east-1 \
    --filters "Name=tag:Name,Values=nautilus-volume" \
    --query 'Volumes[*].[Size,VolumeType]' \
    --output table
```

Expected result:

```text
-----------------------
|  Size  |  Type     |
+--------+-----------+
|  2     |  gp3      |
+--------+-----------+
```

---

# 12. Verify Complete Configuration

The following command displays the main properties required by the challenge:

```bash
aws ec2 describe-volumes \
    --region us-east-1 \
    --filters "Name=tag:Name,Values=nautilus-volume" \
    --query 'Volumes[*].[VolumeId,Size,VolumeType,State,AvailabilityZone]' \
    --output table
```

Expected information:

```text
Volume ID       : vol-xxxxxxxx
Size            : 2
Volume Type     : gp3
State           : available
Availability Zone: us-east-1x
```

The exact Volume ID and Availability Zone depend on the KodeKloud lab environment.

---

# 13. Optional Cleanup

If the lab requires the volume to be deleted after completing the task:

```bash
aws ec2 delete-volume \
    --volume-id <volume-id> \
    --region us-east-1
```

> **Note:** Do not run the cleanup command if the KodeKloud task requires the volume to remain available for verification.

---

# Important Security Notes

Never commit AWS credentials or sensitive files to GitHub.

Do not commit:

```text
AWS Access Key ID
AWS Secret Access Key
AWS Session Token
AWS Console Password
.env
```

If private credential files are present in the project directory, add them to `.gitignore`:

```gitignore
.env
*.pem
*.key
```

---

# Final Verification

The final configuration was:

```text
Volume Name : nautilus-volume
Volume Type : gp3
Volume Size : 2 GiB
AWS Region  : us-east-1
Status      : Successfully Created
```

---

# Day 05 Result

```text
Amazon EBS Volume
       |
       +-- Name: nautilus-volume
       |
       +-- Type: gp3
       |
       +-- Size: 2 GiB
       |
       +-- Region: us-east-1
       |
       +-- Status: Created Successfully
```

**Day 05 — Completed Successfully **

100 Days. 100 Challenges. One Cloud Journey. ☁️🚀
