# Day 30 - Enable Internet Access for Private EC2 Using NAT Instance

## Challenge Overview

This is Day 30 of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The challenge was related to **AWS VPC Networking, NAT Instances, Private Subnets, Route Tables, Security Groups, and S3 Connectivity**.

The Nautilus DevOps team needed to provide internet access to a private EC2 instance without using a NAT Gateway. To reduce infrastructure costs, the team decided to use a **NAT Instance** instead.

The task was to configure a NAT instance in a public subnet and use it to route outbound internet traffic from a private EC2 instance.

The final success condition was that the cron job running on the private EC2 instance successfully uploaded `datacenter-test.txt` to the specified S3 bucket.

### Challenge Requirement

The requirements were:

1. Use the existing VPC named `datacenter-priv-vpc`.
2. Create a public subnet named `datacenter-pub-subnet` with CIDR `10.1.2.0/24`.
3. Configure a public route table with internet access through the Internet Gateway.
4. Associate the public route table with the public subnet.
5. Create a security group named `datacenter-nat-sg`.
6. Launch a NAT EC2 instance named `datacenter-nat-instance` using Amazon Linux 2023.
7. Configure IP forwarding and IP masquerading on the NAT instance.
8. Disable the source/destination check on the NAT instance.
9. Update the private route table to route internet traffic through the NAT instance.
10. Verify that the private EC2 instance can access the internet.
11. Verify that the cron job successfully uploads `datacenter-test.txt` to the S3 bucket `datacenter-nat-147281969`.

---

## Objectives

The objectives of this challenge are:

- Understand NAT Instance architecture.
- Understand the difference between public and private subnets.
- Create and configure a public subnet.
- Configure route tables and subnet associations.
- Understand Internet Gateway functionality.
- Configure security groups for NAT traffic.
- Launch and configure an EC2 NAT instance.
- Enable IP forwarding and IP masquerading.
- Disable EC2 source/destination checks.
- Route private subnet traffic through a NAT instance.
- Verify internet connectivity from a private EC2 instance.
- Verify automated file uploads to Amazon S3.

---

## Environment

| Item | Details |
| ------------------------- | -------------------------------- |
| Challenge | 100 Days of Cloud (AWS) |
| Platform | KodeKloud |
| Day | 30 |
| Cloud Provider | Amazon Web Services (AWS) |
| Main Service | Amazon EC2 |
| Networking Service | Amazon VPC |
| Storage Service | Amazon S3 |
| VPC | `datacenter-priv-vpc` |
| VPC CIDR | `10.1.0.0/16` |
| Public Subnet | `datacenter-pub-subnet` |
| Public Subnet CIDR | `10.1.2.0/24` |
| NAT Security Group | `datacenter-nat-sg` |
| NAT Instance | `datacenter-nat-instance` |
| NAT AMI | Amazon Linux 2023 |
| Private EC2 | `datacenter-priv-ec2` |
| S3 Bucket | `datacenter-nat-147281969` |
| Uploaded File | `datacenter-test.txt` |
| Status | Completed |

---

# Solution

## Step 1: Open the AWS Management Console

First, I accessed the AWS Management Console using the temporary credentials provided by the KodeKloud lab environment.

I selected the required AWS region for the lab and opened the VPC service.

I located the existing VPC:

```text
datacenter-priv-vpc
```

This VPC was already being used by the private EC2 instance.

---

## Step 2: Create the Public Subnet

I navigated to:

```text
AWS Console
    |
    v
    VPC
    |
    v
Subnets
    |
    v
Create Subnet
```

I selected the existing VPC:

```text
datacenter-priv-vpc
```

I configured the subnet with the following details:

```text
Subnet Name: datacenter-pub-subnet
VPC: datacenter-priv-vpc
IPv4 CIDR: 10.1.2.0/24
```

After entering the configuration, I created the subnet.

This public subnet would host the NAT instance.

---

## Step 3: Configure the Public Subnet

After creating the subnet, I enabled automatic assignment of public IPv4 addresses.

Navigation:

```text
VPC
  -> Subnets
  -> datacenter-pub-subnet
  -> Actions
  -> Edit subnet settings
```

I enabled:

```text
Enable auto-assign public IPv4 address
```

This allowed instances launched in the subnet to receive public IPv4 addresses automatically.

---

## Step 4: Configure the Public Route Table

I navigated to the Route Tables section:

```text
VPC
  -> Route Tables
  -> Create route table
```

I created a public route table and associated it with:

```text
VPC: datacenter-priv-vpc
```

I then added the following route:

| Destination | Target |
| ---------------- | ---------------- |
| `10.1.0.0/16` | Local |
| `0.0.0.0/0` | Internet Gateway |

The Internet Gateway was the existing Internet Gateway attached to the VPC.

The default route allowed the NAT instance to communicate with the internet.

Finally, I associated the public route table with:

```text
datacenter-pub-subnet
```

---

## Step 5: Create the NAT Security Group

I opened the EC2 service and navigated to Security Groups.

I created a custom security group with these details:

```text
Security Group Name: datacenter-nat-sg
VPC: datacenter-priv-vpc
Description: Security group for NAT instance
```

I configured the inbound rules to allow traffic originating from the VPC CIDR.

The inbound rule was:

| Type | Protocol | Port | Source |
| -------- | -------- | ---- | ---------- |
| All Traffic | All | All | `10.1.0.0/16` |

For outbound traffic, I allowed the NAT instance to communicate with external destinations.

The security group allowed private instances within the VPC to send traffic through the NAT instance.

---

## Step 6: Launch the NAT Instance

I navigated to:

```text
EC2
  -> Instances
  -> Launch Instances
```

I configured the EC2 instance with the following settings:

```text
Name: datacenter-nat-instance
AMI: Amazon Linux 2023
VPC: datacenter-priv-vpc
Subnet: datacenter-pub-subnet
Security Group: datacenter-nat-sg
```

I selected the appropriate instance type and key pair provided or permitted by the lab environment.

I also ensured that the instance received a public IPv4 address.

---

## Step 7: Configure User Data for NAT Functionality

During instance launch, I provided the user data script to configure the NAT functionality automatically.

The script installed `iptables-services`, enabled IPv4 forwarding, and configured IP masquerading.

The configuration performed the following operations:

1. Installed the iptables services package.
2. Enabled IPv4 packet forwarding.
3. Configured a MASQUERADE rule.
4. Saved the iptables rules.
5. Enabled the iptables service.

The important Linux networking configurations were:

```bash
# Enable IPv4 forwarding
sysctl -w net.ipv4.ip_forward=1

# Configure NAT masquerading
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
```

These configurations allowed the NAT instance to forward private subnet traffic to the internet while using its own public network connection.

---

## Step 8: Disable Source/Destination Check

By default, EC2 instances perform source/destination checks.

However, a NAT instance must forward network traffic that is not directly addressed to itself.

Therefore, I disabled the source/destination check.

Navigation:

```text
EC2
  -> Instances
  -> datacenter-nat-instance
  -> Actions
  -> Networking
  -> Change source/destination check
```

I selected:

```text
Stop
```

This disabled the source/destination check for the NAT instance.

This step was necessary to allow the instance to forward traffic between the private subnet and the internet.

---

## Step 9: Update the Private Route Table

After configuring the NAT instance, I navigated to:

```text
VPC
  -> Route Tables
```

I identified the route table associated with the private subnet containing:

```text
datacenter-priv-ec2
```

I edited the routes and added:

| Destination | Target |
| ---------------- | ------------------------ |
| `0.0.0.0/0` | NAT Instance (`datacenter-nat-instance`) |

The private route table now directed all internet-bound traffic through the NAT instance.

The private EC2 instance could now access external networks without requiring a public IP address.

---

## Step 10: Verify NAT Instance Configuration

I returned to the EC2 Instances dashboard.

I checked that the NAT instance was running:

```text
Instance Name: datacenter-nat-instance
Instance State: Running
```

I also verified:

- The NAT instance was deployed in the public subnet.
- It had a public IPv4 address.
- The correct security group was attached.
- Source/destination checking was disabled.

---

## Step 11: Verify Private EC2 Internet Access

The private EC2 instance was:

```text
datacenter-priv-ec2
```

I verified that its private subnet route table contained the default route pointing to the NAT instance.

This ensured that internet-bound traffic from the private EC2 instance was forwarded through the NAT instance.

---

## Step 12: Verify S3 File Upload

The final success condition was to verify that the cron job on the private EC2 instance uploaded:

```text
datacenter-test.txt
```

to the S3 bucket:

```text
datacenter-nat-147281969
```

I navigated to:

```text
AWS Console
  -> S3
  -> Buckets
  -> datacenter-nat-147281969
```

Inside the bucket, I checked for:

```text
datacenter-test.txt
```

The file appeared successfully.

I also checked its Last Modified timestamp.

The timestamp kept updating, confirming that the cron job was periodically uploading the file.

---

## Step 13: Final Verification

I verified the following components:

| Component | Verification |
| ------------------------- | ------------------------- |
| VPC | `datacenter-priv-vpc` |
| Public Subnet | `datacenter-pub-subnet` |
| Public Subnet CIDR | `10.1.2.0/24` |
| NAT Security Group | `datacenter-nat-sg` |
| NAT Instance | `datacenter-nat-instance` |
| NAT Instance State | Running |
| Source/Destination Check | Disabled |
| Private EC2 | `datacenter-priv-ec2` |
| Private Route Table | Default route to NAT instance |
| S3 Bucket | `datacenter-nat-147281969` |
| Uploaded File | `datacenter-test.txt` |
| Cron Job Upload | Verified through updating timestamp |

---

# NAT Instance Architecture

The overall network architecture for this challenge was:

```text
                 Internet
                    |
                    |
             Internet Gateway
                    |
                    |
          Public Route Table
                    |
                    |
          Public Subnet
           10.1.2.0/24
                    |
                    |
       datacenter-nat-instance
             (NAT Instance)
                    |
                    |
             Private Route
                    |
                    |
          Private Subnet
                    |
                    |
          datacenter-priv-ec2
                    |
                    |
             Internet Access
                    |
                    |
             Amazon S3
                    |
                    |
       datacenter-nat-147281969
                    |
                    |
       datacenter-test.txt
```

---

# NAT Instance vs NAT Gateway

This challenge also helped me understand the difference between a NAT Instance and a NAT Gateway.

| Feature | NAT Instance | NAT Gateway |
| -------------------- | ----------------------------- | ----------------------------- |
| Type | EC2 Instance | Managed AWS Service |
| Management | User managed | AWS managed |
| Configuration | Manual | Easier configuration |
| Security Groups | Supported | Not supported |
| Source/Destination Check | Must be disabled | Managed by AWS |
| Cost | Depends on EC2 resources | NAT Gateway hourly and data processing charges |
| Maintenance | User responsibility | AWS responsibility |

A NAT Instance can be a cost-saving option for suitable workloads, but it requires manual configuration, maintenance, and monitoring.

---

# What I Learned

From this challenge, I learned and practiced:

- How to create a public subnet inside an existing VPC.
- How to configure public and private route tables.
- How an Internet Gateway provides internet connectivity.
- How to configure security groups for NAT traffic.
- How to launch an Amazon Linux EC2 instance as a NAT instance.
- How to enable IPv4 forwarding in Linux.
- How IP masquerading works using iptables.
- Why source/destination checks must be disabled for NAT instances.
- How private EC2 instances access the internet through a NAT instance.
- How to verify S3 uploads from a private EC2 instance.
- How cron jobs can automate periodic file uploads.
- The differences between NAT Instances and NAT Gateways.

# Challenge Status

Day 30 — Completed Successfully ✅

**Created the public subnet, configured the NAT instance, enabled IP forwarding and IP masquerading, disabled source/destination checking, and updated the private route table to provide internet access to `datacenter-priv-ec2`. Verified that `datacenter-test.txt` was successfully uploaded to the S3 bucket `datacenter-nat-147281969` and that its timestamp kept updating.**

100 Days. 100 Challenges. One Cloud Journey. ☁️🚀

I will continue documenting each challenge with practical AWS Console implementations, CLI verification commands, and lessons learned.
