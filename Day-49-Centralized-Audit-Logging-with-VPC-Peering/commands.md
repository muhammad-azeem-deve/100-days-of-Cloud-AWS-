# Day 49 - AWS VPC Peering, EC2, IAM, S3 and Cron Commands

This file contains the AWS CLI commands and Linux commands used for **Day 49** of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The challenge involved creating a secure log aggregation pipeline using AWS VPC Peering, EC2 instances, IAM roles, Amazon S3, SCP, and cron jobs.

---

# 1. Verify AWS CLI Configuration

Check the AWS CLI version:

```bash
aws --version
```

Verify the active AWS identity:

```bash
aws sts get-caller-identity
```

Set the required AWS region:

```bash
export AWS_DEFAULT_REGION=us-east-1
```

---

# 2. Verify the Existing Private VPC

List all VPCs:

```bash
aws ec2 describe-vpcs \
    --region us-east-1
```

Find the existing private VPC:

```bash
aws ec2 describe-vpcs \
    --filters "Name=tag:Name,Values=datacenter-priv-vpc" \
    --region us-east-1
```

Retrieve its CIDR block:

```bash
aws ec2 describe-vpcs \
    --filters "Name=tag:Name,Values=datacenter-priv-vpc" \
    --query 'Vpcs[0].[VpcId,CidrBlock]' \
    --output table \
    --region us-east-1
```

---

# 3. Verify the Existing Private EC2 Instance

Find the private EC2 instance:

```bash
aws ec2 describe-instances \
    --filters "Name=tag:Name,Values=datacenter-priv-ec2" \
    --region us-east-1
```

Retrieve its private IP address:

```bash
aws ec2 describe-instances \
    --filters "Name=tag:Name,Values=datacenter-priv-ec2" \
    --query 'Reservations[0].Instances[0].PrivateIpAddress' \
    --output text \
    --region us-east-1
```

---

# 4. Verify the Log File

Connect to the private EC2 instance and check the log file:

```bash
ls -l /var/log/boots.log
```

Read the file:

```bash
sudo cat /var/log/boots.log
```

---

# 5. Create the Public VPC

Create the new public VPC with a non-overlapping CIDR block:

```bash
aws ec2 create-vpc \
    --cidr-block <PUBLIC-VPC-CIDR> \
    --tag-specifications 'ResourceType=vpc,Tags=[{Key=Name,Value=datacenter-pub-vpc}]' \
    --region us-east-1
```

Save the returned VPC ID:

```text
<PUBLIC-VPC-ID>
```

Verify the VPC:

```bash
aws ec2 describe-vpcs \
    --filters "Name=tag:Name,Values=datacenter-pub-vpc" \
    --region us-east-1
```

---

# 6. Create a Public Subnet

Create a subnet inside the public VPC:

```bash
aws ec2 create-subnet \
    --vpc-id <PUBLIC-VPC-ID> \
    --cidr-block <PUBLIC-SUBNET-CIDR> \
    --availability-zone us-east-1a \
    --tag-specifications 'ResourceType=subnet,Tags=[{Key=Name,Value=datacenter-pub-subnet}]' \
    --region us-east-1
```

Save the subnet ID:

```text
<PUBLIC-SUBNET-ID>
```

Enable automatic public IPv4 address assignment:

```bash
aws ec2 modify-subnet-attribute \
    --subnet-id <PUBLIC-SUBNET-ID> \
    --map-public-ip-on-launch \
    --region us-east-1
```

---

# 7. Create an Internet Gateway

Create an Internet Gateway:

```bash
aws ec2 create-internet-gateway \
    --tag-specifications 'ResourceType=internet-gateway,Tags=[{Key=Name,Value=datacenter-pub-igw}]' \
    --region us-east-1
```

Save the Internet Gateway ID:

```text
<IGW-ID>
```

Attach it to the public VPC:

```bash
aws ec2 attach-internet-gateway \
    --internet-gateway-id <IGW-ID> \
    --vpc-id <PUBLIC-VPC-ID> \
    --region us-east-1
```

Verify the attachment:

```bash
aws ec2 describe-internet-gateways \
    --internet-gateway-ids <IGW-ID> \
    --region us-east-1
```

---

# 8. Create the Public Route Table

Create a route table:

```bash
aws ec2 create-route-table \
    --vpc-id <PUBLIC-VPC-ID> \
    --tag-specifications 'ResourceType=route-table,Tags=[{Key=Name,Value=datacenter-pub-rt}]' \
    --region us-east-1
```

Save the route table ID:

```text
<PUBLIC-RT-ID>
```

Create a default route to the Internet Gateway:

```bash
aws ec2 create-route \
    --route-table-id <PUBLIC-RT-ID> \
    --destination-cidr-block 0.0.0.0/0 \
    --gateway-id <IGW-ID> \
    --region us-east-1
```

Associate the route table with the public subnet:

```bash
aws ec2 associate-route-table \
    --route-table-id <PUBLIC-RT-ID> \
    --subnet-id <PUBLIC-SUBNET-ID> \
    --region us-east-1
```

Verify the routes:

```bash
aws ec2 describe-route-tables \
    --route-table-ids <PUBLIC-RT-ID> \
    --region us-east-1
```

---

# 9. Create and Configure Security Groups

Create a security group for the public EC2 instance:

```bash
aws ec2 create-security-group \
    --group-name datacenter-pub-sg \
    --description "Security group for public EC2 instance" \
    --vpc-id <PUBLIC-VPC-ID> \
    --region us-east-1
```

Save the security group ID:

```text
<PUBLIC-SG-ID>
```

Allow SSH from the private VPC CIDR:

```bash
aws ec2 authorize-security-group-ingress \
    --group-id <PUBLIC-SG-ID> \
    --protocol tcp \
    --port 22 \
    --cidr <PRIVATE-VPC-CIDR> \
    --region us-east-1
```

Allow SSH from the administrator's permitted source if required:

```bash
aws ec2 authorize-security-group-ingress \
    --group-id <PUBLIC-SG-ID> \
    --protocol tcp \
    --port 22 \
    --cidr <ADMIN-PUBLIC-CIDR> \
    --region us-east-1
```

The private EC2 security group must also allow SSH traffic from:

```text
<PUBLIC-VPC-CIDR>
```

---

# 10. Launch the Public EC2 Instance

Launch the EC2 instance using the required key pair:

```bash
aws ec2 run-instances \
    --image-id <UBUNTU-AMI-ID> \
    --instance-type <INSTANCE-TYPE> \
    --key-name datacenter-key \
    --subnet-id <PUBLIC-SUBNET-ID> \
    --security-group-ids <PUBLIC-SG-ID> \
    --associate-public-ip-address \
    --tag-specifications 'ResourceType=instance,Tags=[{Key=Name,Value=datacenter-pub-ec2}]' \
    --region us-east-1
```

Save the instance ID:

```text
<PUBLIC-INSTANCE-ID>
```

Wait for the instance to become ready:

```bash
aws ec2 wait instance-running \
    --instance-ids <PUBLIC-INSTANCE-ID> \
    --region us-east-1
```

Retrieve its public and private IP addresses:

```bash
aws ec2 describe-instances \
    --instance-ids <PUBLIC-INSTANCE-ID> \
    --query 'Reservations[0].Instances[0].[PublicIpAddress,PrivateIpAddress]' \
    --output table \
    --region us-east-1
```

---

# 11. Create the IAM Trust Policy

Create the trust policy file:

```bash
vi ec2-trust-policy.json
```

Add:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "Service": "ec2.amazonaws.com"
            },
            "Action": "sts:AssumeRole"
        }
    ]
}
```

---

# 12. Create the IAM Role

Create the required IAM role:

```bash
aws iam create-role \
    --role-name datacenter-s3-role \
    --assume-role-policy-document file://ec2-trust-policy.json
```

---

# 13. Create the S3 Permissions Policy

Create the policy file:

```bash
vi s3-put-policy.json
```

Add:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:PutObject"
            ],
            "Resource": "arn:aws:s3:::datacenter-s3-logs-711006996/*"
        }
    ]
}
```

---

# 14. Attach the S3 Policy to the IAM Role

Create an inline policy:

```bash
aws iam put-role-policy \
    --role-name datacenter-s3-role \
    --policy-name datacenter-s3-put-policy \
    --policy-document file://s3-put-policy.json
```

Verify the role:

```bash
aws iam get-role \
    --role-name datacenter-s3-role
```

---

# 15. Create an IAM Instance Profile

Create the instance profile:

```bash
aws iam create-instance-profile \
    --instance-profile-name datacenter-s3-role
```

Add the IAM role:

```bash
aws iam add-role-to-instance-profile \
    --instance-profile-name datacenter-s3-role \
    --role-name datacenter-s3-role
```

Wait briefly for IAM propagation before attaching the profile.

---

# 16. Attach the IAM Role to the Public EC2 Instance

Attach the instance profile:

```bash
aws ec2 associate-iam-instance-profile \
    --instance-id <PUBLIC-INSTANCE-ID> \
    --iam-instance-profile Name=datacenter-s3-role \
    --region us-east-1
```

Verify the association:

```bash
aws ec2 describe-iam-instance-profile-associations \
    --region us-east-1
```

---

# 17. Create the Private S3 Bucket

Create the required bucket:

```bash
aws s3api create-bucket \
    --bucket datacenter-s3-logs-711006996 \
    --region us-east-1
```

The bucket is private by default unless public access is explicitly configured.

Enable all S3 Block Public Access settings:

```bash
aws s3api put-public-access-block \
    --bucket datacenter-s3-logs-711006996 \
    --public-access-block-configuration \
    BlockPublicAcls=true,IgnorePublicAcls=true,BlockPublicPolicy=true,RestrictPublicBuckets=true \
    --region us-east-1
```

Verify the bucket:

```bash
aws s3api head-bucket \
    --bucket datacenter-s3-logs-711006996 \
    --region us-east-1
```

---

# 18. Create the VPC Peering Connection

Create the VPC Peering connection:

```bash
aws ec2 create-vpc-peering-connection \
    --vpc-id <PRIVATE-VPC-ID> \
    --peer-vpc-id <PUBLIC-VPC-ID> \
    --tag-specifications 'ResourceType=vpc-peering-connection,Tags=[{Key=Name,Value=datacenter-vpc-peering}]' \
    --region us-east-1
```

Save the peering connection ID:

```text
<PEERING-ID>
```

---

# 19. Accept the VPC Peering Connection

Accept the peering request:

```bash
aws ec2 accept-vpc-peering-connection \
    --vpc-peering-connection-id <PEERING-ID> \
    --region us-east-1
```

Verify its status:

```bash
aws ec2 describe-vpc-peering-connections \
    --vpc-peering-connection-ids <PEERING-ID> \
    --region us-east-1
```

The expected status is:

```text
active
```

---

# 20. Update the Private VPC Route Table

Add a route to the public VPC through the peering connection:

```bash
aws ec2 create-route \
    --route-table-id <PRIVATE-RT-ID> \
    --destination-cidr-block <PUBLIC-VPC-CIDR> \
    --vpc-peering-connection-id <PEERING-ID> \
    --region us-east-1
```

---

# 21. Update the Public VPC Route Table

Add a route to the private VPC through the same peering connection:

```bash
aws ec2 create-route \
    --route-table-id <PUBLIC-RT-ID> \
    --destination-cidr-block <PRIVATE-VPC-CIDR> \
    --vpc-peering-connection-id <PEERING-ID> \
    --region us-east-1
```

Verify both route tables:

```bash
aws ec2 describe-route-tables \
    --route-table-ids <PRIVATE-RT-ID> <PUBLIC-RT-ID> \
    --region us-east-1
```

---

# 22. Configure SSH Key Permissions

On the private EC2 instance, ensure that the private key is available at the configured secure path.

Set the correct permissions:

```bash
chmod 400 /path/to/datacenter-key.pem
```

Test connectivity to the public EC2 instance:

```bash
ssh -i /path/to/datacenter-key.pem \
    ubuntu@<PUBLIC-PRIVATE-IP>
```

Use the private IP address rather than the public IP address for VPC Peering communication.

---

# 23. Configure SCP Log Transfer

On the private EC2 instance, test the SCP command:

```bash
scp -i /path/to/datacenter-key.pem \
    /var/log/boots.log \
    ubuntu@<PUBLIC-PRIVATE-IP>:/home/ubuntu/boots.log
```

This transfers the log file to the public EC2 instance.

---

# 24. Configure the First Cron Job

Open the private instance's crontab:

```bash
crontab -e
```

Add the scheduled transfer:

```cron
*/5 * * * * /usr/bin/scp -i /path/to/datacenter-key.pem -o StrictHostKeyChecking=no /var/log/boots.log ubuntu@<PUBLIC-PRIVATE-IP>:/home/ubuntu/boots.log
```

Verify the cron job:

```bash
crontab -l
```

---

# 25. Verify the Received Log File

Connect to the public EC2 instance and check:

```bash
ls -l /home/ubuntu/boots.log
```

Read the transferred file:

```bash
cat /home/ubuntu/boots.log
```

---

# 26. Verify IAM Role Access on Public EC2

On the public EC2 instance, verify the IAM identity:

```bash
aws sts get-caller-identity
```

The returned identity should correspond to the assumed `datacenter-s3-role`.

Check the attached instance role through the EC2 metadata service if needed.

---

# 27. Upload the Log File to S3

Run the following command on the public EC2 instance:

```bash
aws s3 cp /home/ubuntu/boots.log \
    s3://datacenter-s3-logs-711006996/datacenter-priv-vpc/boot/boots.log
```

This uploads the file to the required S3 object key.

---

# 28. Configure the Second Cron Job

Open the public instance's crontab:

```bash
crontab -e
```

Add the scheduled S3 upload:

```cron
*/5 * * * * /usr/bin/aws s3 cp /home/ubuntu/boots.log s3://datacenter-s3-logs-711006996/datacenter-priv-vpc/boot/boots.log
```

Verify the cron configuration:

```bash
crontab -l
```

---

# 29. Verify the Cron Service

Check the cron service on Ubuntu:

```bash
sudo systemctl status cron
```

If it is not running:

```bash
sudo systemctl start cron
```

Enable it at system startup:

```bash
sudo systemctl enable cron
```

---

# 30. Verify the Uploaded S3 Object

List the destination folder:

```bash
aws s3 ls \
    s3://datacenter-s3-logs-711006996/datacenter-priv-vpc/boot/
```

Check the exact object:

```bash
aws s3api head-object \
    --bucket datacenter-s3-logs-711006996 \
    --key datacenter-priv-vpc/boot/boots.log
```

Download the file for an additional verification if required:

```bash
aws s3 cp \
    s3://datacenter-s3-logs-711006996/datacenter-priv-vpc/boot/boots.log \
    ./boots.log
```

---

# Important Notes

- Replace placeholder values such as `<PUBLIC-VPC-CIDR>`, `<PRIVATE-VPC-CIDR>`, and `<PEERING-ID>` with the actual values obtained from AWS.
- Use non-overlapping CIDR blocks for the two VPCs.
- Both VPC route tables must have routes pointing to the peering connection.
- Security groups must allow SSH traffic between the VPC CIDR ranges.
- The private EC2 instance must have access to the SSH private key required for SCP.
- The public EC2 instance must have the correct IAM instance profile attached.
- The S3 bucket should remain private.
- Do not hardcode AWS access keys or secret keys on EC2 instances.
- The cron commands must use the correct absolute paths and Linux usernames.
- Ensure the private instance can read `/var/log/boots.log` when the cron job executes.

---

# Day 49 - Final Result

```text
Private VPC       : datacenter-priv-vpc
Private EC2       : datacenter-priv-ec2

Public VPC        : datacenter-pub-vpc
Public EC2        : datacenter-pub-ec2

Key Pair          : datacenter-key

IAM Role          : datacenter-s3-role
IAM Permission    : s3:PutObject

S3 Bucket         : datacenter-s3-logs-711006996

VPC Peering       : datacenter-vpc-peering
Peering Status    : Active

Log Source        : /var/log/boots.log
Transfer Method   : SCP
Automation        : Cron Jobs

S3 Destination    : datacenter-priv-vpc/boot/boots.log

AWS Region        : us-east-1
Status            : Successfully Completed
```

**Day 49 — Completed Successfully ✅**

100 Days. 100 Challenges. One Cloud Journey. ☁️🚀
