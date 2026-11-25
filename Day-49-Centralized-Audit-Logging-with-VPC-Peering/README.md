# Day 49 - Secure Log Aggregation Pipeline Using AWS

## Challenge Overview

This is Day 49 of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The challenge was related to **AWS VPC Networking, VPC Peering, EC2, IAM Roles, Amazon S3, SCP, and Linux Cron Jobs**.

The Nautilus DevOps team wanted to build a secure and automated log aggregation pipeline using AWS services.

An existing private VPC contained an Ubuntu EC2 instance that stored application logs. The task was to create a separate public VPC with another EC2 instance, establish connectivity between both VPCs using VPC Peering, and configure automated log transfers from the private instance to an Amazon S3 bucket.

The pipeline required two scheduled jobs:

1. A cron job on the private EC2 instance to transfer the log file to the public EC2 instance using SCP.
2. A cron job on the public EC2 instance to upload the received log file to a private S3 bucket using AWS CLI.

The final objective was to ensure that the log file was automatically uploaded to the correct S3 object path.

### Challenge Requirement

The requirements were:

1. Use the AWS `us-east-1` region.
2. Use the existing private VPC named `datacenter-priv-vpc`.
3. Use the existing private EC2 instance named `datacenter-priv-ec2`.
4. Locate the log file `/var/log/boots.log` on the private EC2 instance.
5. Create a new public VPC named `datacenter-pub-vpc`.
6. Create a public subnet inside the new VPC.
7. Create and configure a route table for the public subnet.
8. Create and attach an Internet Gateway to the public VPC.
9. Configure the public subnet to access the internet.
10. Launch a public EC2 instance named `datacenter-pub-ec2`.
11. Use the existing `datacenter-key` key pair.
12. Create an IAM role named `datacenter-s3-role`.
13. Grant the IAM role permission to upload objects to Amazon S3 using `s3:PutObject`.
14. Attach the IAM role to the public EC2 instance through an instance profile.
15. Create a private S3 bucket named `datacenter-s3-logs-711006996`.
16. Create a VPC Peering connection named `datacenter-vpc-peering`.
17. Update both VPC route tables to allow communication between the two VPCs through the peering connection.
18. Configure an SCP-based log transfer from the private EC2 instance to the public EC2 instance.
19. Configure a cron job on the private instance to automate the log transfer.
20. Configure another cron job on the public instance to upload the log file to S3.
21. Ensure the final log file is available at:

```text
s3://datacenter-s3-logs-711006996/datacenter-priv-vpc/boot/boots.log
```

---

## Objectives

The objectives of this challenge are:

- Understand AWS VPC networking.
- Create and configure a public VPC.
- Understand public and private subnets.
- Configure Internet Gateways and route tables.
- Establish connectivity between two VPCs using VPC Peering.
- Launch EC2 instances in different VPCs.
- Configure security groups for private communication.
- Create IAM roles and attach permissions.
- Understand EC2 instance profiles.
- Create and configure a private S3 bucket.
- Transfer files between EC2 instances using SCP.
- Automate tasks using Linux cron jobs.
- Upload files to S3 using AWS CLI.
- Build an automated cloud-based log aggregation pipeline.

---

## Environment

| Item | Details |
|---|---|
| Challenge | 100 Days of Cloud (AWS) |
| Platform | KodeKloud |
| Day | 49 |
| Cloud Provider | Amazon Web Services |
| Region | `us-east-1` |
| Existing Private VPC | `datacenter-priv-vpc` |
| Private EC2 | `datacenter-priv-ec2` |
| Log File | `/var/log/boots.log` |
| New Public VPC | `datacenter-pub-vpc` |
| Public EC2 | `datacenter-pub-ec2` |
| Key Pair | `datacenter-key` |
| IAM Role | `datacenter-s3-role` |
| S3 Bucket | `datacenter-s3-logs-711006996` |
| VPC Peering | `datacenter-vpc-peering` |
| Transfer Method | SCP |
| Automation | Cron Jobs |
| Destination Object | `datacenter-priv-vpc/boot/boots.log` |
| Status | Completed |

---

# Solution

## Step 1: Access the AWS Management Console

First, I accessed the AWS Management Console using the temporary credentials provided by the KodeKloud lab environment.

I made sure that the selected AWS region was:

```text
us-east-1
```

All the resources for this challenge had to be created in this region.

I also verified the AWS CLI identity from the AWS client machine:

```bash
aws sts get-caller-identity
```

## Step 2: Verify the Existing Private VPC

Before creating the new infrastructure, I checked the existing private VPC.

The existing VPC was:

```text
datacenter-priv-vpc
```

I navigated to:

```text
AWS Console
    |
    v
VPC
    |
    v
Your VPCs
```

I verified the VPC details, including its IPv4 CIDR block.

The existing private EC2 instance was:

```text
datacenter-priv-ec2
```

This instance contained the required log file:

```text
/var/log/boots.log
```

I connected to the private instance and verified that the file existed:

```bash
ls -l /var/log/boots.log
```

I also checked its contents:

```bash
sudo cat /var/log/boots.log
```

## Step 3: Create the Public VPC

Next, I created a new VPC for the public EC2 instance.

The required VPC name was:

```text
datacenter-pub-vpc
```

I navigated to:

```text
VPC Dashboard
    |
    v
Your VPCs
    |
    v
Create VPC
```

I configured the VPC with the required name and a non-overlapping IPv4 CIDR block.

The public VPC needed a CIDR block that did not overlap with the existing private VPC.

## Step 4: Create a Public Subnet

After creating the public VPC, I created a subnet inside it.

The subnet was associated with:

```text
datacenter-pub-vpc
```

I configured an IPv4 CIDR block within the public VPC CIDR range.

I also enabled automatic assignment of public IPv4 addresses for instances launched in this subnet.

This allowed the public EC2 instance to receive a public IPv4 address.

## Step 5: Create an Internet Gateway

To provide internet connectivity to the public VPC, I created an Internet Gateway.

The navigation path was:

```text
VPC Dashboard
    |
    v
Internet Gateways
    |
    v
Create Internet Gateway
```

After creating the Internet Gateway, I attached it to:

```text
datacenter-pub-vpc
```

An Internet Gateway provides a route between the public VPC and the internet when the appropriate routing and public addressing are configured.

## Step 6: Create and Configure the Public Route Table

Next, I created a route table for the public VPC.

I associated the route table with the public subnet.

I added the following default route:

| Destination | Target |
|---|---|
| Public VPC CIDR | local |
| `0.0.0.0/0` | Internet Gateway |

The default route allowed instances in the public subnet to communicate with the internet.

## Step 7: Configure Security Groups

I configured the security groups to allow the required communication between the two EC2 instances.

The important communication requirement was SSH access over the VPC Peering connection.

The required security group rules included:

**Public EC2 Security Group**

- Allow SSH traffic on TCP port 22 from the private VPC CIDR.
- Allow SSH traffic from the administrator's permitted source when required.

**Private EC2 Security Group**

- Allow SSH traffic on TCP port 22 from the public VPC CIDR.

These rules allowed the two instances to communicate securely through their private IP addresses.

## Step 8: Launch the Public EC2 Instance

After completing the public VPC networking configuration, I launched a new EC2 instance.

The required instance name was:

```text
datacenter-pub-ec2
```

I configured the instance with:

```text
Instance Name : datacenter-pub-ec2
VPC          : datacenter-pub-vpc
Subnet       : Public Subnet
Key Pair     : datacenter-key
```

I selected an appropriate Ubuntu AMI as required by the lab.

After launching the instance, I verified that it received a public IPv4 address.

I also verified that the instance was running successfully.

## Step 9: Create the IAM Role

The public EC2 instance needed permission to upload log files to Amazon S3.

Instead of storing AWS access keys on the EC2 instance, I created an IAM role.

The required role name was:

```text
datacenter-s3-role
```

I configured the trust relationship to allow the EC2 service to assume the role.

The trusted service was:

```text
ec2.amazonaws.com
```

I created a permissions policy that allowed the following action:

```text
s3:PutObject
```

The permission was restricted to the required S3 bucket.

Example policy:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "s3:PutObject",
            "Resource": "arn:aws:s3:::datacenter-s3-logs-711006996/*"
        }
    ]
}
```

This policy allowed the EC2 instance to upload objects to the specified bucket without requiring manually configured AWS access keys.

## Step 10: Create an Instance Profile and Attach the IAM Role

An EC2 instance uses an instance profile to receive an IAM role.

I ensured that the `datacenter-s3-role` IAM role was available through an instance profile.

I then attached the role to:

```text
datacenter-pub-ec2
```

After attaching the role, I verified that the instance could obtain temporary AWS credentials through the EC2 instance metadata service.

## Step 11: Create the Private S3 Bucket

Next, I created the S3 bucket to store the collected log files.

The required bucket name was:

```text
datacenter-s3-logs-711006996
```

I navigated to:

```text
AWS Console
    |
    v
Amazon S3
    |
    v
Create Bucket
```

I configured the bucket in the `us-east-1` region.

I kept the bucket private by maintaining the appropriate Block Public Access settings.

The bucket was used as the centralized destination for the log aggregation pipeline.

## Step 12: Create the VPC Peering Connection

To enable private communication between the two VPCs, I created a VPC Peering connection.

The required peering connection name was:

```text
datacenter-vpc-peering
```

The VPCs involved were:

| Configuration | VPC |
|---|---|
| Requester VPC | `datacenter-priv-vpc` |
| Accepter VPC | `datacenter-pub-vpc` |
| Peering Name | `datacenter-vpc-peering` |

I selected the two VPCs and created the peering connection.

After creation, I accepted the peering request.

The connection status became:

```text
Active
```

## Step 13: Update the Private VPC Route Table

After establishing the peering connection, I updated the route table associated with the private VPC.

I added a route pointing to the public VPC CIDR block through the peering connection.

The routing configuration was:

| Destination | Target |
|---|---|
| Public VPC CIDR | `datacenter-vpc-peering` |

This allowed the private EC2 instance to communicate with the public EC2 instance through the peering connection.

## Step 14: Update the Public VPC Route Table

I also updated the public VPC route table.

I added a route pointing to the private VPC CIDR block through the same peering connection.

The routing configuration was:

| Destination | Target |
|---|---|
| Private VPC CIDR | `datacenter-vpc-peering` |

Both route tables now contained routes for communication between the two VPCs.

## Step 15: Verify VPC Connectivity

After updating both route tables, I verified the connectivity between the two EC2 instances.

I used their private IPv4 addresses for communication.

From the private EC2 instance, I tested SSH connectivity to the public EC2 instance:

```bash
ssh -i datacenter-key.pem ubuntu@<PUBLIC-PRIVATE-IP>
```

The SSH connection required the correct private key and security group permissions.

Successful connectivity confirmed that the peering connection and routing configuration were working.

## Step 16: Configure SSH Key Access for SCP

The private EC2 instance needed to transfer the log file to the public EC2 instance.

For this purpose, I configured SSH key-based authentication.

The private key associated with the `datacenter-key` key pair was made securely available to the private instance.

I ensured that the private key had restricted permissions:

```bash
chmod 400 datacenter-key.pem
```

I also verified SSH connectivity between the instances before configuring the scheduled transfer.

## Step 17: Configure the First Cron Job on the Private EC2 Instance

The first cron job was configured on:

```text
datacenter-priv-ec2
```

Its purpose was to copy the log file:

```text
/var/log/boots.log
```

to the public EC2 instance using SCP.

The destination was the public instance's private IP address.

Example SCP command:

```bash
scp -i /path/to/datacenter-key.pem \
    /var/log/boots.log \
    ubuntu@<PUBLIC-PRIVATE-IP>:/home/ubuntu/boots.log
```

I added the transfer command to the private instance's crontab.

To edit the cron configuration:

```bash
crontab -e
```

Example scheduled entry:

```cron
*/5 * * * * /usr/bin/scp -i /path/to/datacenter-key.pem -o StrictHostKeyChecking=no /var/log/boots.log ubuntu@<PUBLIC-PRIVATE-IP>:/home/ubuntu/boots.log
```

This scheduled the log transfer every five minutes.

The exact schedule and key path should match the configuration used in the lab.

## Step 18: Configure the Second Cron Job on the Public EC2 Instance

The second cron job was configured on:

```text
datacenter-pub-ec2
```

Its purpose was to upload the received log file to the S3 bucket.

The destination object path was:

```text
datacenter-priv-vpc/boot/boots.log
```

I used AWS CLI to upload the file:

```bash
aws s3 cp /home/ubuntu/boots.log \
    s3://datacenter-s3-logs-711006996/datacenter-priv-vpc/boot/boots.log
```

I added the command to the public instance's crontab:

```bash
crontab -e
```

Example scheduled entry:

```cron
*/5 * * * * /usr/bin/aws s3 cp /home/ubuntu/boots.log s3://datacenter-s3-logs-711006996/datacenter-priv-vpc/boot/boots.log
```

The public EC2 instance used the attached IAM role to authenticate with AWS and upload the file.

## Step 19: Verify the Cron Jobs

I verified the scheduled cron jobs on both instances.

To list the cron jobs:

```bash
crontab -l
```

I also checked that the cron service was running:

```bash
sudo systemctl status cron
```

The private instance was responsible for transferring the log file using SCP.

The public instance was responsible for uploading the received file to S3.

## Step 20: Verify the S3 Object

Finally, I verified that the log file was successfully uploaded to the S3 bucket.

I used:

```bash
aws s3 ls s3://datacenter-s3-logs-711006996/datacenter-priv-vpc/boot/
```

The expected object was:

```text
boots.log
```

I also verified the exact S3 object using:

```bash
aws s3api head-object \
    --bucket datacenter-s3-logs-711006996 \
    --key datacenter-priv-vpc/boot/boots.log
```

This confirmed that the log file was available in the required S3 location.

---

# Log Aggregation Architecture

The overall architecture of this challenge was:

```text
              AWS Cloud
                  |
       +----------+----------+
       |                     |
       v                     v
 Private VPC              Public VPC
       |                     |
       |                     |
       v                     v
datacenter-priv-ec2   datacenter-pub-ec2
       |                     |
       |                     |
 /var/log/boots.log          |
       |                     |
       |                     |
       +---- SCP Transfer -->|
       |                     |
       |                AWS CLI Upload
       |                     |
       |                     v
       |                Amazon S3
       |                     |
       |                     v
       |          datacenter-s3-logs-711006996
       |                     |
       |                     v
       |       datacenter-priv-vpc/boot/boots.log
       |
       +------ VPC Peering ------+
```

### Automation Workflow

```text
Private EC2 Instance
        |
        v
    Cron Job
        |
        v
  Read boots.log
        |
        v
 Transfer using SCP
        |
        v
Public EC2 Instance
        |
        v
    Cron Job
        |
        v
  AWS S3 CP Command
        |
        v
 Private S3 Bucket
        |
        v
   boots.log Uploaded
```

---

# Final Verification

The final infrastructure configuration was:

| Resource | Name / Configuration | Status |
|---|---|---|
| Private VPC | `datacenter-priv-vpc` | Existing |
| Private EC2 | `datacenter-priv-ec2` | Existing |
| Log File | `/var/log/boots.log` | Available |
| Public VPC | `datacenter-pub-vpc` | Created |
| Public EC2 | `datacenter-pub-ec2` | Created |
| Key Pair | `datacenter-key` | Configured |
| IAM Role | `datacenter-s3-role` | Created |
| IAM Permission | `s3:PutObject` | Configured |
| S3 Bucket | `datacenter-s3-logs-711006996` | Created |
| VPC Peering | `datacenter-vpc-peering` | Active |
| Private Route Table | Route to Public VPC | Configured |
| Public Route Table | Route to Private VPC | Configured |
| Private Cron Job | SCP Log Transfer | Configured |
| Public Cron Job | S3 Log Upload | Configured |
| Final S3 Object | `datacenter-priv-vpc/boot/boots.log` | Verified |
| AWS Region | `us-east-1` | Confirmed |

---

# What I Learned

From this challenge, I learned and practiced:

- How to create and configure AWS VPCs.
- How to create public subnets.
- How Internet Gateways provide internet connectivity.
- How to configure public route tables.
- How to launch EC2 instances in different VPCs.
- How to establish connectivity using VPC Peering.
- How to configure routes between peered VPCs.
- How to configure security groups for SSH communication.
- How to create IAM roles for EC2 instances.
- How to grant restricted S3 permissions using IAM policies.
- How EC2 instance profiles provide temporary AWS credentials.
- How to create private S3 buckets.
- How to transfer files between EC2 instances using SCP.
- How to automate Linux tasks using cron jobs.
- How to upload files to S3 using AWS CLI.
- How to build an automated log aggregation pipeline.

# Challenge Status

Day 49 — Completed Successfully ✅

**Created the `datacenter-pub-vpc` and `datacenter-pub-ec2`, configured the `datacenter-s3-role` IAM role, established connectivity between the private and public VPCs using `datacenter-vpc-peering`, and automated log transfers using SCP and cron jobs. Successfully uploaded `boots.log` to the required private S3 bucket at `datacenter-priv-vpc/boot/boots.log`.**

100 Days. 100 Challenges. One Cloud Journey. ☁️🚀

I will continue documenting each challenge and building my practical AWS cloud engineering skills step by step.
