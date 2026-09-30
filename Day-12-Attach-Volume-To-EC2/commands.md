# Day 12 - EBS Volume Attachment Verification Commands

This file contains **only the AWS CLI verification commands** used for Day 12 of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The EBS volume was attached to the EC2 instance using the **AWS Management Console**.

---

## 1. Verify AWS Identity

```bash
aws sts get-caller-identity
```

---

## 2. Verify the EBS Volume

```bash
aws ec2 describe-volumes \
    --region us-east-1 \
    --query 'Volumes[*].[VolumeId,State,Size,VolumeType,AvailabilityZone]' \
    --output table
```

---

## 3. Verify Attached Volumes

```bash
aws ec2 describe-volumes \
    --region us-east-1 \
    --filters Name=status,Values=in-use \
    --query 'Volumes[*].[VolumeId,State,Size,VolumeType,AvailabilityZone]' \
    --output table
```

---

## 4. Verify Volume Attachment Details

```bash
aws ec2 describe-volumes \
    --region us-east-1 \
    --query 'Volumes[*].Attachments[*].[VolumeId,InstanceId,Device,State]' \
    --output table
```

---

## 5. Verify EC2 Instance and Attached Volume

```bash
aws ec2 describe-instances \
    --region us-east-1 \
    --query 'Reservations[*].Instances[*].[InstanceId,State.Name,BlockDeviceMappings[*].[DeviceName,Ebs.VolumeId,Ebs.Status]]' \
    --output table
```

---

## 6. Verify Volumes Attached to Running Instances

```bash
aws ec2 describe-instances \
    --region us-east-1 \
    --filters Name=instance-state-name,Values=running \
    --query 'Reservations[*].Instances[*].BlockDeviceMappings[*].[Ebs.VolumeId,DeviceName,Ebs.Status]' \
    --output table
```

---

## 7. Verify Volume Attachment State

```bash
aws ec2 describe-volumes \
    --region us-east-1 \
    --query 'Volumes[*].Attachments[*].State' \
    --output table
```

Expected attachment state:

```text
attached
```

---

## 8. Verify Volume State

```bash
aws ec2 describe-volumes \
    --region us-east-1 \
    --query 'Volumes[*].State' \
    --output table
```

Expected state for an attached volume:

```text
in-use
```

---

## 9. Verify Instance IDs Associated with Volumes

```bash
aws ec2 describe-volumes \
    --region us-east-1 \
    --query 'Volumes[*].Attachments[*].InstanceId' \
    --output table
```

---

## 10. Final Attachment Verification

```bash
aws ec2 describe-volumes \
    --region us-east-1 \
    --query 'Volumes[?State==`in-use`].{VolumeId:VolumeId,State:State,InstanceId:Attachments[0].InstanceId,Device:Attachments[0].Device,AttachmentState:Attachments[0].State}' \
    --output table
```

Expected information:

```text
VolumeId       State     InstanceId       Device       AttachmentState
-------------  --------  ---------------  -----------  ----------------
vol-xxxxxxxx   in-use    i-xxxxxxxxxxxxx  /dev/sdf    attached
```

---

# Day 12 Verification Result

```text
Region           : us-east-1
Volume State     : in-use
Attachment State : attached
EC2 Instance     : Attached Successfully
```

**Day 12 — Verification Completed Successfully**
