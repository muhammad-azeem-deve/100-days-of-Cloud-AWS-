# Day 27 - Create a Public VPC, Subnet, and EC2 Instance

## Challenge Overview

This is Day 27 of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The challenge was related to **Amazon VPC, Public Subnets, Automatic Public IP Assignment, EC2 Instances, and Security Groups**.

The task was to create a public Virtual Private Cloud (VPC), configure a subnet to automatically assign public IP addresses, and launch an EC2 instance inside that subnet with SSH access enabled.

The purpose of this challenge was to understand how AWS networking resources work together to provide network connectivity to an EC2 instance.

### Challenge Requirement

The requirements were:

1. Create a VPC named `devops-pub-vpc`.
2. Create a subnet named `devops-pub-subnet` inside the VPC.
3. Enable automatic public IPv4 address assignment for the subnet.
4. Create an EC2 instance named `devops-pub-ec2`.
5. Select the EC2 instance type `t2.micro`.
6. Enable SSH access on port `22`.
7. Create all resources in the required AWS region, `us-east-1`.
8. Verify the configuration using the AWS Management Console and AWS CLI.

---

## Objectives

The objectives of this challenge are:

- Understand Amazon VPC and subnet concepts.
- Create a custom VPC.
- Create a subnet inside a VPC.
- Enable automatic public IPv4 assignment.
- Configure a security group for SSH access.
- Launch an EC2 instance inside a specific subnet.
- Understand the relationship between VPCs, subnets, route tables, and internet gateways.
- Verify AWS resources using the console and AWS CLI.

---

## Environment

| Item | Details |
|---|---|
| Challenge | 100 Days of Cloud (AWS) |
| Platform | KodeKloud |
| Day | 27 |
| Cloud Provider | Amazon Web Services (AWS) |
| Region | `us-east-1` |
| VPC Name | `devops-pub-vpc` |
| Subnet Name | `devops-pub-subnet` |
| EC2 Instance Name | `devops-pub-ec2` |
| Instance Type | `t2.micro` |
| Subnet Type | Public subnet configuration |
| Automatic Public IP Assignment | Enabled |
| SSH Port | `22` |
| Status | Completed after verification |

---

# Solution

All resources were created using the **AWS Management Console**.

## Step 1: Open the AWS Management Console

First, I logged in to the AWS Management Console using the temporary credentials provided by the KodeKloud lab.

I selected the required region:

```text
US East (N. Virginia) - us-east-1
```

It was important to use the correct region because the challenge required all resources to be created in `us-east-1`.

---

## Step 2: Create the VPC

I opened the VPC service from the AWS Management Console.

Navigation:

```text
AWS Console
    |
    v
VPC
    |
    v
Your VPCs
    |
    v
Create VPC
```

I configured the VPC with the following settings:

| Setting | Value |
|---|---|
| Resources to create | VPC only |
| Name tag | `devops-pub-vpc` |
| IPv4 CIDR block | `10.0.0.0/16` |
| IPv6 CIDR block | No IPv6 CIDR block |
| Tenancy | Default |

I selected **Create VPC**.

The VPC was created with the name `devops-pub-vpc`.

> Note: The IPv4 CIDR block above is an example configuration. Use a non-overlapping CIDR block if the lab specifies a different value or has existing network ranges that conflict.

### Verify the VPC

I opened **Your VPCs** and confirmed that:

- The VPC name was `devops-pub-vpc`.
- The IPv4 CIDR block was configured.
- The VPC state was `Available`.

---

## Step 3: Create the Subnet

Next, I opened the Subnets section.

Navigation:

```text
VPC
  |
  v
Subnets
  |
  v
Create subnet
```

I selected the VPC:

```text
devops-pub-vpc
```

I configured the subnet as follows:

| Setting | Value |
|---|---|
| Subnet name | `devops-pub-subnet` |
| VPC | `devops-pub-vpc` |
| Availability Zone | Any available AZ in `us-east-1` |
| IPv4 subnet CIDR block | `10.0.1.0/24` |

I selected **Create subnet**.

### Verify the Subnet

I opened the Subnets page and checked that:

- The subnet name was `devops-pub-subnet`.
- The subnet belonged to `devops-pub-vpc`.
- The subnet state was `Available`.

---

## Step 4: Enable Automatic Public IP Assignment

After creating the subnet, I selected `devops-pub-subnet`.

I opened:

```text
Actions
   |
   v
Edit subnet settings
```

I enabled the option:

```text
Enable auto-assign public IPv4 address
```

I saved the changes.

This setting allows eligible EC2 instances launched into the subnet to receive a public IPv4 address automatically, unless the launch configuration overrides it.

### Verify Automatic Public IP Assignment

I returned to the subnet details and verified that automatic public IPv4 address assignment was enabled.

---

## Step 5: Configure Internet Connectivity for the Public Subnet

A subnet is not publicly reachable merely because automatic public IP assignment is enabled. It also needs a route to an internet gateway.

I checked the VPC's networking configuration.

### Create or Verify an Internet Gateway

I opened:

```text
VPC
  |
  v
Internet gateways
```

If an appropriate internet gateway was not already available, I created one and named it:

```text
devops-pub-igw
```

I attached it to:

```text
devops-pub-vpc
```

### Configure the Route Table

I opened **Route tables** and selected the route table associated with `devops-pub-subnet`.

I verified that it contained a route similar to:

| Destination | Target |
|---|---|
| `10.0.0.0/16` | local |
| `0.0.0.0/0` | Internet gateway (`igw-...`) |

If the default route was missing, I added a route with destination `0.0.0.0/0` and the attached internet gateway as its target.

I also verified that the route table was associated with `devops-pub-subnet`.

This configuration provides a route to the internet. Actual connectivity also depends on security group rules, network ACLs, and the instance's public IPv4 address.

---

## Step 6: Configure the Security Group

I opened the EC2 service and navigated to **Security Groups**.

I created or selected a security group associated with `devops-pub-vpc`.

The inbound rule was configured as follows:

| Type | Protocol | Port | Source |
|---|---|---|---|
| SSH | TCP | `22` | My IP address or the lab-required CIDR |

The outbound rules were left at the lab's required settings.

SSH access on port 22 allows authorized SSH clients to attempt to connect to the instance.

For better security, I used **My IP** where the lab allowed it rather than opening SSH to everyone.

---

## Step 7: Launch the EC2 Instance

I opened:

```text
EC2
  |
  v
Instances
  |
  v
Launch instances
```

I configured the instance with the following settings:

| Setting | Value |
|---|---|
| Name | `devops-pub-ec2` |
| Amazon Machine Image | An available Linux AMI |
| Instance type | `t2.micro` |
| VPC | `devops-pub-vpc` |
| Subnet | `devops-pub-subnet` |
| Auto-assign public IP | Enabled |
| Security group | SSH inbound rule on port `22` |
| Key pair | An available lab key pair, or create one if required |

I reviewed the configuration and selected **Launch instance**.

The instance was launched inside the newly created VPC and subnet.

---

## Step 8: Verify the EC2 Instance

I opened the EC2 Instances page and selected `devops-pub-ec2`.

I checked the following details:

- Instance name: `devops-pub-ec2`
- Instance type: `t2.micro`
- Instance state: `Running`
- VPC: `devops-pub-vpc`
- Subnet: `devops-pub-subnet`
- Public IPv4 address: Assigned
- Security group: SSH access enabled on port `22`

I also checked the instance's networking details to ensure it was attached to the intended VPC and subnet.

---

# Verification Using the AWS Console

I verified the resources in the AWS Management Console.

| Resource | Verification Location | Expected Result |
|---|---|---|
| VPC | VPC → Your VPCs | `devops-pub-vpc` |
| Subnet | VPC → Subnets | `devops-pub-subnet` |
| Auto-assign public IP | Subnet settings | Enabled |
| Internet gateway | VPC → Internet gateways | Attached to the VPC |
| Route table | VPC → Route tables | Default route to the internet gateway |
| Security group | EC2 → Security Groups | SSH/TCP port `22` allowed |
| EC2 instance | EC2 → Instances | `devops-pub-ec2` |
| Instance type | Instance details | `t2.micro` |
| Instance state | Instance details | Running |
| Public IPv4 | Instance networking details | Assigned |

---

# Verification Using AWS CLI

The `commands.md` file contains only verification commands.

These commands check the VPC, subnet settings, internet gateway, route table, security group, and EC2 instance without creating or modifying any resources.

Run the commands using AWS CLI credentials authorized for the KodeKloud lab account.

The region is explicitly set to `us-east-1` in the commands.

---

# What I Learned

From this challenge, I learned and practiced:

- How to create a VPC using the AWS Management Console.
- How to create a subnet inside a custom VPC.
- How to enable automatic public IPv4 address assignment.
- Why a public subnet needs an internet gateway route.
- How to configure a route table.
- How to enable SSH access using a security group.
- How to launch an EC2 instance into a specific subnet.
- How VPCs, subnets, route tables, internet gateways, and security groups work together.
- How to verify AWS resources using the AWS Console and CLI.

# Challenge Status

Day 27 — Completed Successfully ✅

**Created the `devops-pub-vpc` VPC, the `devops-pub-subnet` subnet with automatic public IPv4 assignment, and the `devops-pub-ec2` EC2 instance of type `t2.micro`, with SSH access enabled on port `22`.**

100 Days. 100 Challenges. One Cloud Journey. ☁️🚀
