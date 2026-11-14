# Day 45 - Configure NAT Gateway for Internet Access in a Private VPC

## Challenge Overview

This is Day 45 of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The challenge was related to **AWS VPC Networking, NAT Gateway, Internet Gateway, Route Tables, Elastic IP, and Private Subnet Internet Connectivity**.

The Nautilus DevOps team needed to provide outbound internet access to an EC2 instance running inside a private subnet. The instance was named `devops-priv-ec2`.

The EC2 instance needed internet connectivity to allow its scheduled cron job to upload a test file to an Amazon S3 bucket.

Since instances in a private subnet should not be directly accessible from the internet, I configured a **NAT Gateway** in a public subnet to provide outbound internet connectivity.

The existing infrastructure already included a VPC, private subnet, and running EC2 instance.

### Challenge Requirement

The requirements were:

1. Create a public subnet named `devops-pub-subnet` in the existing VPC.
2. Create an Internet Gateway and attach it to the VPC.
3. Create a route table named `devops-pub-rt`.
4. Add a default route (`0.0.0.0/0`) through the Internet Gateway.
5. Associate the public route table with the public subnet.
6. Allocate an Elastic IP address.
7. Create a NAT Gateway named `devops-natgw` in the public subnet.
8. Create a dedicated private route table named `devops-priv-rt`.
9. Add a default route (`0.0.0.0/0`) through the NAT Gateway.
10. Explicitly associate the private route table with the private subnet.
11. Wait for the NAT Gateway to become available.
12. Verify that the private EC2 instance can access the internet by checking the test file in the S3 bucket.

---

## Objectives

The objectives of this challenge are:

- Understand AWS VPC networking.
- Understand the difference between public and private subnets.
- Create and configure an Internet Gateway.
- Understand the purpose of NAT Gateways.
- Allocate and associate Elastic IP addresses.
- Create and configure custom route tables.
- Configure public subnet internet connectivity.
- Configure private subnet outbound internet connectivity.
- Understand default routes and route table associations.
- Verify internet access from a private EC2 instance.
- Understand how private instances can communicate with external AWS services.

---

## Environment

| Item | Details |
| ------------------------- | ------------------------------ |
| Challenge | 100 Days of Cloud (AWS) |
| Platform | KodeKloud |
| Day | 45 |
| Cloud Provider | Amazon Web Services (AWS) |
| Region | us-east-1 |
| VPC | `devops-priv-vpc` |
| VPC CIDR | `10.1.0.0/16` |
| Private Subnet | `devops-priv-subnet` |
| Private Subnet CIDR | `10.1.1.0/24` |
| Public Subnet | `devops-pub-subnet` |
| Internet Gateway | Created and attached |
| Public Route Table | `devops-pub-rt` |
| Private Route Table | `devops-priv-rt` |
| NAT Gateway | `devops-natgw` |
| Elastic IP | Allocated |
| EC2 Instance | `devops-priv-ec2` |
| S3 Bucket | `devops-nat-55134834` |
| Test File | `devops-test.txt` |
| Status | Completed Successfully |

---

# Solution

## Step 1: Access the AWS Management Console

First, I accessed the AWS Management Console using the temporary credentials provided by the KodeKloud lab environment.

I selected the required AWS region:

```text
us-east-1
```

Then I opened the **VPC** service to configure the networking infrastructure.

---

## Step 2: Identify the Existing VPC

The VPC was already created with the following configuration:

```text
VPC Name: devops-priv-vpc
CIDR:     10.1.0.0/16
```

I verified the existing VPC and confirmed that the private subnet was already available.

The existing private subnet configuration was:

```text
Subnet Name: devops-priv-subnet
CIDR:        10.1.1.0/24
```

The EC2 instance `devops-priv-ec2` was also already running inside this private subnet.

---

## Step 3: Create the Public Subnet

I created a new public subnet inside the existing VPC.

Configuration:

```text
Subnet Name: devops-pub-subnet
VPC:         devops-priv-vpc
CIDR:        10.1.2.0/24
```

The public subnet was created within the same VPC as the private subnet.

This subnet would host the NAT Gateway.

---

## Step 4: Create an Internet Gateway

An Internet Gateway (IGW) provides communication between resources in a VPC and the public internet when the appropriate routing and network configurations are in place.

I navigated to:

```text
VPC
  -> Internet Gateways
  -> Create Internet Gateway
```

I created a new Internet Gateway and attached it to:

```text
VPC: devops-priv-vpc
```

After attachment, the Internet Gateway was available for use in the public route table.

---

## Step 5: Create the Public Route Table

I created a new route table:

```text
Route Table Name: devops-pub-rt
VPC:              devops-priv-vpc
```

I then added the following default route:

| Destination | Target |
| ---------------- | ---------------- |
| `10.1.0.0/16` | Local |
| `0.0.0.0/0` | Internet Gateway |

The default route directs internet-bound traffic through the Internet Gateway.

---

## Step 6: Associate the Public Route Table

After creating the public route table, I explicitly associated it with:

```text
devops-pub-subnet
```

This association ensures that resources in the public subnet use the route table containing the Internet Gateway route.

---

## Step 7: Allocate an Elastic IP Address

A NAT Gateway requires an Elastic IP address to communicate with the public internet.

I navigated to:

```text
VPC
  -> Elastic IPs
  -> Allocate Elastic IP address
```

I allocated a new Elastic IP address.

The allocated Elastic IP was used when creating the NAT Gateway.

---

## Step 8: Create the NAT Gateway

I navigated to:

```text
VPC
  -> NAT Gateways
  -> Create NAT Gateway
```

I configured the NAT Gateway using the following settings:

```text
Name:             devops-natgw
Subnet:           devops-pub-subnet
Connectivity:     Public
Elastic IP:       Allocated Elastic IP
```

After creating the NAT Gateway, I waited for its status to change to:

```text
Available
```

The NAT Gateway was now ready to handle outbound internet traffic from the private subnet.

---

## Step 9: Create the Private Route Table

Instead of modifying the existing Main route table, I created a separate route table specifically for the private subnet.

Configuration:

```text
Route Table Name: devops-priv-rt
VPC:              devops-priv-vpc
```

This dedicated route table allows the private subnet to use the NAT Gateway without affecting other subnets in the VPC.

---

## Step 10: Add the NAT Gateway Route

I added the following default route to `devops-priv-rt`:

| Destination | Target |
| ---------------- | ---------------- |
| `10.1.0.0/16` | Local |
| `0.0.0.0/0` | NAT Gateway (`devops-natgw`) |

The route:

```text
0.0.0.0/0 -> NAT Gateway
```

ensures that outbound traffic from the private subnet is forwarded through the NAT Gateway.

---

## Step 11: Associate the Private Route Table

After configuring the route table, I explicitly associated it with the existing private subnet:

```text
Private Route Table: devops-priv-rt
Private Subnet:      devops-priv-subnet
```

This step was important because simply creating a route table does not automatically make it active for the intended subnet.

The private subnet now used the NAT Gateway for internet-bound traffic.

I did not modify the VPC Main route table.

---

## Step 12: Verify NAT Gateway Configuration

I checked the NAT Gateway configuration and confirmed:

```text
NAT Gateway Name: devops-natgw
Subnet:           devops-pub-subnet
Connectivity:     Public
Elastic IP:       Allocated
State:            Available
```

I also verified that both route tables had the correct default routes.

**Public Route Table:**

```text
0.0.0.0/0 -> Internet Gateway
```

**Private Route Table:**

```text
0.0.0.0/0 -> NAT Gateway
```

---

## Step 13: Wait for NAT Gateway Initialization

After configuring the networking resources, I waited approximately 2–3 minutes.

This allowed the NAT Gateway to become fully available and the updated routing configuration to take effect.

---

## Step 14: Verify Internet Connectivity Through S3

The existing EC2 instance `devops-priv-ec2` had a scheduled cron job responsible for uploading a test file to the S3 bucket.

The target S3 bucket was:

```text
devops-nat-55134834
```

The expected test file was:

```text
devops-test.txt
```

After waiting 2–3 minutes, I opened the Amazon S3 service and navigated to the bucket:

```text
devops-nat-55134834
```

I checked the bucket contents and confirmed that the following file was present:

```text
devops-test.txt
```

This confirmed that the private EC2 instance could successfully communicate with Amazon S3 through the NAT Gateway.

---

# Network Architecture

The overall network architecture configured in this challenge was:

```text
                  Internet
                     |
                     |
              Internet Gateway
                     |
                     |
              Public Route Table
               devops-pub-rt
                     |
              0.0.0.0/0 -> IGW
                     |
                     |
              Public Subnet
              devops-pub-subnet
                 10.1.2.0/24
                     |
                     |
               NAT Gateway
               devops-natgw
                     |
                Elastic IP
                     |
                     |
              Private Route Table
               devops-priv-rt
                     |
              0.0.0.0/0 -> NAT
                     |
                     |
              Private Subnet
             devops-priv-subnet
                 10.1.1.0/24
                     |
                     |
                EC2 Instance
              devops-priv-ec2
                     |
                     |
                Cron Job
                     |
                     |
              Amazon S3 Bucket
             devops-nat-55134834
                     |
                     |
              devops-test.txt
```

---

# Traffic Flow

The outbound internet traffic followed this path:

1. The cron job on `devops-priv-ec2` initiated an outbound request.
2. The private subnet forwarded the traffic using `devops-priv-rt`.
3. The private route table forwarded the traffic to `devops-natgw`.
4. The NAT Gateway translated the private instance's source address using its Elastic IP.
5. The public route table forwarded the traffic through the Internet Gateway.
6. The request reached the destination.
7. The response returned through the NAT Gateway to the private EC2 instance.

The private EC2 instance did not require a public IP address to initiate outbound internet communication.

---

# What I Learned

From this challenge, I learned and practiced:

- How to work with an existing AWS VPC.
- How to create public and private subnets.
- How an Internet Gateway provides internet connectivity.
- How to allocate an Elastic IP address.
- How to create and configure a public NAT Gateway.
- How NAT Gateways provide outbound internet access to private instances.
- How to create custom route tables.
- How to configure default routes using `0.0.0.0/0`.
- How to associate route tables with specific subnets.
- Why public and private subnets require different routing configurations.
- Why a dedicated private route table is useful.
- How to verify private subnet connectivity using an S3 upload.
- How AWS networking components work together to provide secure outbound connectivity.

# Challenge Status

Day 45 — Completed Successfully ✅

**Created the public subnet, attached an Internet Gateway, allocated an Elastic IP, configured the `devops-natgw` NAT Gateway, and created dedicated public and private route tables. Successfully verified outbound internet connectivity from `devops-priv-ec2` when `devops-test.txt` appeared in the S3 bucket `devops-nat-55134834`.**

100 Days. 100 Challenges. One Cloud Journey. ☁️🚀

I will continue documenting each AWS challenge with practical implementation steps, configuration details, verification, and lessons learned.
