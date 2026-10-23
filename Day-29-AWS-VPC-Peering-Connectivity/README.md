# Day 29 - AWS VPC Peering Connection

## Challenge Overview

This is Day 29 of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The challenge was related to **Amazon VPC Peering, Route Tables, Network Connectivity, and EC2 Communication**.

The task was to establish a VPC peering connection between the default VPC and a private VPC so that the public EC2 instance could communicate with the private EC2 instance.

The connection was configured using the AWS Management Console, and the network connectivity was verified using the AWS CLI and a ping test from the public EC2 instance.

### Challenge Requirement

The requirements were:

1. Use the default VPC with CIDR block `172.31.0.0/16`.
2. Use the `xfusion-private-vpc` VPC with CIDR block `10.1.0.0/16`.
3. Create a VPC peering connection named `xfusion-vpc-peering`.
4. Accept the VPC peering connection.
5. Configure routes in both VPC route tables.
6. Enable communication between `xfusion-public-ec2` and `xfusion-private-ec2`.
7. Verify connectivity to the private EC2 instance at `10.1.1.62`.

---

## Objectives

The objectives of this challenge are:

- Understand Amazon VPC peering.
- Connect two VPCs using a peering connection.
- Configure VPC peering through the AWS Management Console.
- Understand CIDR blocks and private IP addresses.
- Configure routes in both VPC route tables.
- Troubleshoot SSH and network connectivity issues.
- Verify communication between EC2 instances in different VPCs.
- Understand the difference between network connectivity and SSH authentication.

---

## Environment

| Item | Details |
|---|---|
| Challenge | 100 Days of Cloud (AWS) |
| Platform | KodeKloud |
| Day | 29 |
| Cloud Service | Amazon VPC |
| Compute Service | Amazon EC2 |
| Peering Connection | `xfusion-vpc-peering` |
| Requester VPC | Default VPC |
| Requester CIDR | `172.31.0.0/16` |
| Accepter VPC | `xfusion-private-vpc` |
| Accepter CIDR | `10.1.0.0/16` |
| Public EC2 Instance | `xfusion-public-ec2` |
| Private EC2 Instance | `xfusion-private-ec2` |
| Private EC2 IP | `10.1.1.62` |
| Implementation | AWS Management Console |
| Verification | AWS Console and AWS CLI |
| Status | Completed |

---

# Solution

## Step 1: Open the AWS Management Console

First, I accessed the AWS Management Console using the credentials provided by the KodeKloud lab environment.

I selected the required AWS region:

```text
us-east-1
```

All resources and network configurations were managed in this region.

---

## Step 2: Inspect the Existing VPCs

I opened the Amazon VPC service and navigated to **Your VPCs**.

I inspected the existing VPCs and confirmed the required network ranges.

### Default VPC

```text
VPC: Default VPC
CIDR: 172.31.0.0/16
```

### Private VPC

```text
VPC Name: xfusion-private-vpc
CIDR: 10.1.0.0/16
```

These CIDR ranges do not overlap, so they can be connected using VPC peering.

---

## Step 3: Create the VPC Peering Connection

From the VPC Console, I navigated to:

```text
VPC
  └── Peering connections
        └── Create peering connection
```

I configured the peering connection using the following details:

```text
Name: xfusion-vpc-peering
Requester: Default VPC
Accepter: xfusion-private-vpc
```

I selected the default VPC as the requester and `xfusion-private-vpc` as the accepter.

After confirming the configuration, I created the peering connection.

---

## Step 4: Accept the VPC Peering Request

After creating the connection, I selected `xfusion-vpc-peering` from the Peering connections list.

I chose **Actions → Accept request** to accept the peering connection.

I verified that its status changed to:

```text
Active
```

An active peering connection establishes the relationship between the VPCs, but route table configuration is also required for communication.

---

## Step 5: Configure the Default VPC Route Table

I opened the **Route tables** section in the VPC Console.

I identified the route table associated with the subnet containing `xfusion-public-ec2`.

I selected the route table and opened:

```text
Routes → Edit routes → Add route
```

I added the route for the private VPC:

```text
Destination: 10.1.0.0/16
Target:      xfusion-vpc-peering
```

I saved the changes.

This route directs traffic destined for the private VPC through the peering connection.

---

## Step 6: Configure the Private VPC Route Table

Next, I identified the route table associated with the subnet containing `xfusion-private-ec2`.

I added the route for the default VPC:

```text
Destination: 172.31.0.0/16
Target:      xfusion-vpc-peering
```

I saved the changes.

Both route tables now had routes for the other VPC's CIDR block.

**Important:** The route tables must be the ones associated with the relevant EC2 subnets. Updating an unrelated route table will not provide the intended connectivity.

---

## Step 7: Verify EC2 Security Groups

I inspected the security groups associated with both EC2 instances.

For the public instance, I checked that the outbound rules allowed traffic to the private VPC.

For the private instance, I checked that inbound rules allowed the required traffic from the public VPC or the appropriate source security group.

For ICMP testing, the private instance's security group needed an inbound rule permitting the relevant ICMP traffic from the public VPC's network range.

For SSH access, TCP port `22` needed to be allowed from the appropriate source.

Network ACLs also needed to permit the required traffic.

---

## Step 8: Access the Public EC2 Instance

I used the available lab access method to connect to:

```text
xfusion-public-ec2
```

I verified that I was connected to the correct instance before testing connectivity to the private EC2 instance.

The private IP address of the destination was:

```text
10.1.1.62
```

---

## Step 9: Troubleshoot SSH Connectivity

During testing, I encountered the following issue:

```text
SSH connection timed out
```

A timeout can indicate a routing, security group, network ACL, or other connectivity problem.

After network connectivity was restored, SSH returned:

```text
Permission denied (publickey)
```

This indicated that the connection reached the SSH service but public-key authentication failed.

I investigated the available key-based authentication and used an available access method to reach the instance.

**Lesson learned:** Successful network routing does not automatically guarantee successful SSH authentication. The correct private key and authorized public key are still required.

---

## Step 10: Resolve the Ping Command Issue

Initially, the ping command was unavailable on the `aws-client` machine:

```text
ping: command not found
```

Instead of relying on the client machine, I ran the connectivity test from `xfusion-public-ec2`, which was the intended source of the network traffic.

From the public EC2 instance, I executed:

```bash
ping -c 4 10.1.1.62
```

The test returned four successful replies with zero packet loss.

This confirmed that ICMP traffic could travel between the two EC2 instances through the configured network path.

---

## Step 11: Verify the VPC Peering Connection in the Console

I returned to the AWS VPC Console and inspected **Peering connections**.

I verified that:

```text
Name:   xfusion-vpc-peering
Status: Active
```

I also reviewed both route tables and confirmed that the routes pointed to the VPC peering connection.

---

## Step 12: Verify Network Connectivity

The final connectivity test was performed from the public EC2 instance to the private EC2 instance.

Command:

```bash
ping -c 4 10.1.1.62
```

The test result showed:

```text
4 packets transmitted, 4 received, 0% packet loss
```

This confirmed successful ICMP connectivity between the instances.

---

# Network Architecture

The following diagram represents the intended network configuration:

```text
       Default VPC
     172.31.0.0/16
            |
            |
   xfusion-public-ec2
            |
            |
     VPC Peering
 xfusion-vpc-peering
         (Active)
            |
            |
   xfusion-private-vpc
      10.1.0.0/16
            |
            |
   xfusion-private-ec2
       10.1.1.62
```

### Routing Configuration

| Route Table | Destination | Target |
|---|---|---|
| Default VPC route table | `10.1.0.0/16` | VPC peering connection |
| Private VPC route table | `172.31.0.0/16` | VPC peering connection |

---

# Troubleshooting

| Issue | Explanation | Resolution |
|---|---|---|
| SSH connection timed out | Network path or port access was unavailable | Checked routes, security groups, and network ACLs |
| `Permission denied (publickey)` | SSH authentication failed | Investigated key-based authentication and used an available access method |
| `ping: command not found` | Ping utility was unavailable on the client | Ran the test from the public EC2 instance |
| No connectivity between VPCs | Peering alone does not configure routes | Added routes in both VPC route tables |

---

# What I Learned

From this challenge, I learned and practiced:

- How to create a VPC peering connection.
- How to accept a VPC peering request.
- How to connect two VPCs with non-overlapping CIDR ranges.
- How to configure routes for VPC peering.
- How security groups and network ACLs affect connectivity.
- How to troubleshoot SSH timeouts.
- How to distinguish SSH authentication failures from network failures.
- How to test private IP connectivity using ICMP.
- How to verify AWS networking resources through the Console and CLI.

# Challenge Status

Day 29 — Completed Successfully ✅

**Created and activated `xfusion-vpc-peering` between the default VPC (`172.31.0.0/16`) and `xfusion-private-vpc` (`10.1.0.0/16`), configured routes in both VPC route tables, and verified connectivity to `xfusion-private-ec2` at `10.1.1.62` with four successful ping replies and zero packet loss.**

100 Days. 100 Challenges. One Cloud Journey. ☁️🚀

I will continue documenting each challenge and building my practical AWS cloud skills step by step.
