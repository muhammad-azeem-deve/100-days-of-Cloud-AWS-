# Day 40 - Troubleshooting Internet Accessibility for an EC2-Hosted Application

## Challenge Overview

This is Day 40 of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The challenge was related to **AWS Networking, EC2 Troubleshooting, VPC Configuration, Internet Gateways, and Route Tables**.

The Nautilus Development Team recently deployed a web application hosted on an EC2 instance inside a public VPC named `nautilus-vpc`.

The application was running on an **Nginx web server** and was supposed to be accessible from the internet through HTTP on port `80`.

However, despite configuring the Security Group to allow HTTP traffic and verifying the EC2 instance settings, the application was not accessible from the internet.

The task was to troubleshoot the VPC networking configuration, identify the root cause, and restore internet accessibility to the application.

### Challenge Requirement

The requirements were:

1. Access the AWS Management Console.
2. Navigate to the VPC service.
3. Inspect the VPC named `nautilus-vpc`.
4. Verify the EC2 instance named `nautilus-ec2`.
5. Check the Security Group named `nautilus-sg` and verify HTTP access on port `80`.
6. Inspect the subnet and associated route table.
7. Identify the Internet Gateway configuration issue.
8. Attach the existing Internet Gateway `nautilus-ig` to `nautilus-vpc`.
9. Verify that the route table has an active route to the Internet Gateway.
10. Access the Nginx application through the EC2 public IPv4 address.

---

## Objectives

The objectives of this challenge are:

- Understand AWS VPC networking.
- Troubleshoot internet connectivity issues in EC2.
- Understand the purpose of an Internet Gateway.
- Inspect VPC and subnet configurations.
- Understand public and private subnets.
- Verify route table configurations.
- Identify and resolve an unattached Internet Gateway.
- Understand the relationship between EC2, VPC, Subnets, Route Tables, and Security Groups.
- Verify application accessibility through a web browser.

---

## Environment

| Item | Details |
| ----------------- | ------------------------------ |
| Challenge | 100 Days of Cloud (AWS) |
| Platform | KodeKloud |
| Day | 40 |
| Cloud Provider | Amazon Web Services (AWS) |
| Region | `us-east-1` |
| VPC | `nautilus-vpc` |
| EC2 Instance | `nautilus-ec2` |
| Security Group | `nautilus-sg` |
| Internet Gateway | `nautilus-ig` |
| Subnet | `nautilus-subnet` |
| Route Table | `nautilus-rtb` |
| Web Server | Nginx |
| Application Port | `80` |
| Status | Completed |

---

# Solution

## Step 1: Access the AWS Management Console

First, I accessed the AWS Management Console using the temporary credentials provided by the KodeKloud lab environment.

I selected the required AWS region:

```text
us-east-1
```

All resources and troubleshooting activities were performed in this region.

---

## Step 2: Open the EC2 Dashboard

First, I navigated to the Amazon EC2 service.

The navigation path was:

```text
AWS Management Console
        |
        v
       EC2
        |
        v
   Instances
```

I located the EC2 instance named:

```text
nautilus-ec2
```

I selected the instance and checked its configuration.

I verified the following:

- Instance state was Running.
- Instance had a public IPv4 address.
- The correct VPC was selected.
- The associated Security Group was `nautilus-sg`.

The EC2 instance configuration appeared correct, so I continued investigating the networking configuration.

---

## Step 3: Verify the Security Group

Next, I inspected the Security Group associated with the EC2 instance.

The Security Group name was:

```text
nautilus-sg
```

I navigated to:

```text
EC2
  -> Network & Security
  -> Security Groups
```

I selected `nautilus-sg` and opened the Inbound Rules tab.

The Security Group already allowed HTTP traffic on port `80`.

The required inbound rule was:

| Type | Protocol | Port Range | Source |
|------|----------|------------|--------|
| HTTP | TCP | 80 | 0.0.0.0/0 |

Since HTTP traffic was already allowed, the Security Group was not the root cause of the problem.

---

## Step 4: Open the VPC Dashboard

After verifying the EC2 instance and Security Group, I moved to the Amazon VPC service.

Navigation path:

```text
AWS Management Console
        |
        v
       VPC
        |
        v
      Your VPCs
```

I located the VPC:

```text
nautilus-vpc
```

I selected the VPC and inspected its networking configuration.

---

## Step 5: Inspect the VPC Resource Map

Inside the VPC dashboard, I opened the Resource Map for `nautilus-vpc`.

The resource map helped me understand the relationships between:

- VPC
- Subnets
- Route Tables
- Internet Gateway
- EC2 networking resources

During inspection, I identified that the Internet Gateway was not properly attached to the VPC.

This explained why the application was not reachable from the internet.

---

## Step 6: Inspect the Route Table

Next, I navigated to:

```text
VPC
  -> Route Tables
```

I selected the route table:

```text
nautilus-rtb
```

I opened the Routes tab and inspected the configured routes.

The route table contained a default route:

```text
Destination: 0.0.0.0/0
Target:      Internet Gateway
```

However, the Internet Gateway route was showing:

```text
State: Blackhole
```

### What does Blackhole mean?

A Blackhole route means that the route points to a target that is unavailable or no longer valid.

In this case, the route table referenced an Internet Gateway that was not attached to the VPC.

Therefore, the subnet could not use that Internet Gateway to communicate with the internet.

---

## Step 7: Inspect Internet Gateways

To identify the exact problem, I navigated to:

```text
VPC
  -> Internet Gateways
```

I located the existing Internet Gateway:

```text
nautilus-ig
```

After selecting it, I inspected its state.

The Internet Gateway was showing:

```text
State: Detached
```

This confirmed the root cause.

The Internet Gateway existed, and the route table referenced it, but it was not attached to `nautilus-vpc`.

As a result, the EC2 instance could not receive internet traffic through the VPC.

---

## Step 8: Attach the Internet Gateway to the VPC

To resolve the problem, I selected:

```text
nautilus-ig
```

Then I followed these steps:

1. Opened the Actions menu.
2. Selected **Attach to VPC**.
3. Selected the target VPC:

```text
nautilus-vpc
```

4. Clicked **Attach internet gateway**.

The Internet Gateway was successfully attached to the VPC.

The final configuration was:

```text
Internet Gateway: nautilus-ig
VPC:              nautilus-vpc
State:            Attached
```

---

## Step 9: Verify the Route Table

After attaching the Internet Gateway, I returned to:

```text
VPC
  -> Route Tables
  -> nautilus-rtb
  -> Routes
```

I inspected the default route again.

Previously, the route was showing:

```text
Destination: 0.0.0.0/0
Target:      Internet Gateway
State:       Blackhole
```

After attaching the Internet Gateway, the route became:

```text
Destination: 0.0.0.0/0
Target:      nautilus-ig
State:       Active
```

This confirmed that the route table could now use the Internet Gateway.

---

## Step 10: Verify the VPC Resource Map

I returned to the VPC Resource Map and inspected the updated networking configuration.

The Internet Gateway was now attached to:

```text
nautilus-vpc
```

The route table was correctly associated with the subnet and had an active route to the Internet Gateway.

The VPC networking configuration was now correct for internet access.

---

## Step 11: Test the Nginx Application

Finally, I returned to the EC2 dashboard.

I selected:

```text
nautilus-ec2
```

From the instance details, I copied its Public IPv4 address.

I opened a web browser and entered:

```text
http://<EC2-PUBLIC-IP>
```

The Nginx welcome page appeared successfully.

This confirmed that the application was accessible from the internet through HTTP on port `80`.

---

# Root Cause Analysis

The application was inaccessible because the Internet Gateway was detached from the VPC.

Although the Security Group allowed HTTP traffic and the route table contained a default route, the route was in a Blackhole state because its Internet Gateway was not attached.

### Problem Identified

```text
Internet Gateway: nautilus-ig
Previous State:   Detached

Route Table:      nautilus-rtb
Previous State:   Blackhole
```

### Solution Applied

```text
Internet Gateway: nautilus-ig
New State:        Attached

VPC:              nautilus-vpc

Route Table:      nautilus-rtb
New Route State:  Active
```

---

# Networking Workflow

The overall networking flow after fixing the issue was:

```text
             Internet
                 |
                 v
          Internet Gateway
            nautilus-ig
                 |
                 v
             nautilus-vpc
                 |
                 v
          Route Table
           nautilus-rtb
                 |
                 v
          nautilus-subnet
                 |
                 v
            EC2 Instance
            nautilus-ec2
                 |
                 v
          Security Group
            nautilus-sg
                 |
                 v
           Nginx Server
                 |
                 v
             Port 80
                 |
                 v
          Nginx Welcome Page
```

---

# What I Learned

From this challenge, I learned and practiced:

- How to troubleshoot EC2 internet connectivity issues.
- How to inspect VPC configurations using the AWS Console.
- How to inspect VPC Resource Maps.
- How to verify Security Group inbound rules.
- How to inspect subnet route table associations.
- What an Internet Gateway is and why it is required.
- How to identify a Blackhole route.
- How to attach an existing Internet Gateway to a VPC.
- How an Internet Gateway changes from Detached to Attached.
- How to verify that a route becomes Active.
- How to test an Nginx application using an EC2 public IPv4 address.
- How different AWS networking components work together to provide internet access.

# Challenge Status

Day 40 — Completed Successfully ✅

**Identified the detached Internet Gateway as the root cause of the EC2 internet connectivity problem, attached `nautilus-ig` to `nautilus-vpc`, verified that the route in `nautilus-rtb` became Active, and successfully accessed the Nginx application through the EC2 public IPv4 address.**

100 Days. 100 Challenges. One Cloud Journey. ☁️🚀

I will continue documenting each challenge and building my practical AWS cloud skills step by step.
