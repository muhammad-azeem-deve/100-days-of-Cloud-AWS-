# Day 26 - Configure an EC2 Instance as an Nginx Web Server

## Challenge Overview

This is Day 26 of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The challenge was related to **Amazon EC2, User Data Scripts, Nginx Installation, and Web Server Configuration**.

The Nautilus DevOps team wanted to deploy a web server in AWS as part of its cloud migration strategy. The task was to create an EC2 instance and configure it to automatically install and start the Nginx web server using a startup script.

Instead of manually installing Nginx after connecting to the server, I used the **User Data** feature during instance creation to automate the installation and startup process.

### Challenge Requirement

The requirements were:

1. Access the AWS Management Console.
2. Use the `us-east-1` AWS region.
3. Launch an EC2 instance using a suitable Linux AMI.
4. Configure the required security group rules.
5. Add a User Data script to install and start Nginx.
6. Launch the EC2 instance and wait for initialization.
7. Verify that the instance is running.
8. Access the Nginx web server using the instance's public IPv4 address.
9. Verify the deployment using the AWS Console and AWS CLI.

---

## Objectives

The objectives of this challenge are:

- Understand the basics of Amazon EC2.
- Launch a virtual server using the AWS Management Console.
- Configure a security group for HTTP access.
- Understand EC2 User Data.
- Automate software installation using a shell script.
- Install and start Nginx automatically during instance initialization.
- Access a web server through its public IP address.
- Verify EC2 instance details using the AWS CLI.
- Understand the importance of cloud automation.

---

## Environment

| Item | Details |
|---|---|
| Challenge | 100 Days of Cloud (AWS) |
| Platform | KodeKloud |
| Day | 26 |
| Cloud Provider | Amazon Web Services (AWS) |
| Service | Amazon EC2 |
| Region | `us-east-1` |
| Operating System | Amazon Linux |
| Web Server | Nginx |
| Installation Method | EC2 User Data |
| HTTP Port | `80` |
| Access Method | Public IPv4 address |
| Status | Completed |

---

# Solution

## Step 1: Access the AWS Management Console

First, I logged into the AWS Management Console using the credentials provided by the KodeKloud lab.

I selected the required AWS region:

```text
us-east-1
```

All resources for this challenge were created in the specified region.

---

## Step 2: Open Amazon EC2

From the AWS Management Console, I opened the EC2 service.

The navigation path was:

```text
AWS Management Console
        |
        v
      EC2
        |
        v
  Launch Instance
```

I selected **Launch instance** to begin configuring the web server.

---

## Step 3: Configure the EC2 Instance

I entered the required instance configuration in the EC2 launch wizard.

The configuration included:

```text
Operating System : Amazon Linux
Instance Type    : A lab-supported instance type
Region           : us-east-1
Public IP        : Enabled
```

I used a suitable Amazon Linux AMI and an instance type supported by the lab.

I also configured the required key pair if SSH access was needed.

---

## Step 4: Configure the Security Group

I configured the EC2 security group to allow incoming HTTP traffic.

The required inbound rule was:

| Type | Protocol | Port | Source |
|---|---|---|---|
| HTTP | TCP | 80 | `0.0.0.0/0` |

This rule allows users to access the Nginx web server through a browser.

If SSH access was required for troubleshooting, I would also allow TCP port `22` from my own IP address.

**Security note:** HTTP access from anywhere is suitable for a public demonstration website, but SSH should not normally be exposed to everyone.

---

## Step 5: Add the User Data Script

In the EC2 launch wizard, I expanded **Advanced details** and located the **User data** field.

I entered the following shell script to install and start Nginx automatically.

```bash
#!/bin/bash

dnf update -y
dnf install -y nginx
systemctl enable nginx
systemctl start nginx
```

### Explanation of the Script

- `#!/bin/bash` specifies the shell used to execute the script.
- `dnf update -y` updates available packages.
- `dnf install -y nginx` installs Nginx on Amazon Linux 2023.
- `systemctl enable nginx` configures Nginx to start automatically when the instance boots.
- `systemctl start nginx` starts the Nginx web server immediately.

The script is intended for **Amazon Linux 2023**. If the lab uses a different operating system, the package installation commands may need to be adjusted.

EC2 User Data scripts normally execute during the instance's initial launch, so I allowed time for the installation to finish before testing the website. <Link url="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/user-data.html" title="AWS User Data documentation"/>.

---

## Step 6: Launch the Instance

After configuring the instance, security group, and User Data script, I reviewed the settings.

I then selected **Launch instance**.

AWS created the EC2 instance and started the initialization process.

I waited until the instance reached the following state:

```text
Instance State : Running
Status Checks : 2/2 checks passed
```

The Nginx installation could take additional time after the instance entered the running state.

---

## Step 7: Verify the EC2 Instance in the Console

I navigated to:

```text
EC2
  -> Instances
  -> Select the created instance
```

I checked the following details:

- Instance state was `Running`.
- System and instance status checks passed.
- Public IPv4 address was assigned.
- The correct security group was attached.
- The instance was launched in `us-east-1`.

I copied the instance's public IPv4 address for browser testing.

---

## Step 8: Access the Nginx Web Server

I opened a browser and entered the instance's public IPv4 address:

```text
http://<EC2-PUBLIC-IP>
```

For example, replace the placeholder with the actual public IPv4 address shown in the EC2 Console.

If the installation completed successfully and the security group allowed HTTP traffic, the browser displayed the default Nginx welcome page.

This confirmed that the web server was reachable over HTTP.

---

## Step 9: Verify the Deployment

I performed the following checks:

1. Confirmed the EC2 instance was running in the console.
2. Confirmed the instance status checks passed.
3. Confirmed that the public IPv4 address was available.
4. Confirmed that inbound TCP port `80` was allowed.
5. Opened the public IP address in a browser.
6. Verified that the Nginx welcome page appeared.

I also prepared AWS CLI verification commands in `commands.md` to check the instance state, public IP, security group rules, and HTTP response.

---

# Architecture Workflow

The overall workflow for this challenge was:

```text
AWS Management Console
        |
        v
   Amazon EC2
        |
        v
 Configure Instance
        |
        v
 Configure Security Group
        |
        v
 Add User Data Script
        |
        v
    Launch EC2
        |
        v
 Execute Startup Script
        |
        v
   Install Nginx
        |
        v
   Start Nginx
        |
        v
 Allow HTTP on Port 80
        |
        v
 Open Public IPv4 Address
        |
        v
 Nginx Welcome Page
```

---

# Troubleshooting

If the Nginx page does not open, I would check the following:

### 1. Check Instance State

Make sure the EC2 instance is running and its status checks have passed.

### 2. Check the Security Group

Confirm that inbound TCP port `80` is allowed from the required source.

### 3. Check the Public IP Address

Make sure I am using the correct public IPv4 address and that the instance has a public IP.

### 4. Allow Time for User Data

The instance can become running before the installation script finishes. Wait a few minutes and test again.

### 5. Inspect User Data Logs

If SSH or an appropriate management connection is available, check the cloud-init output log:

```bash
sudo cat /var/log/cloud-init-output.log
```

On the instance, verify the Nginx service:

```bash
sudo systemctl status nginx
```

These checks help identify installation failures or network configuration problems.

---

# What I Learned

From this challenge, I learned and practiced:

- How to launch an EC2 instance through the AWS Console.
- How to configure a Linux instance for web hosting.
- How to configure security group inbound rules.
- How to use EC2 User Data to automate server configuration.
- How to install Nginx using a startup script.
- How to enable and start a Linux system service.
- How to access a web server using its public IPv4 address.
- How to verify EC2 resources using the AWS CLI.
- How to troubleshoot web server connectivity issues.
- Why automation is important when deploying cloud infrastructure.

# Challenge Status

Day 26 — Completed Successfully 

**Configured an EC2 instance as an Nginx web server using a User Data startup script, enabled HTTP access through the security group, and verified the deployment using the AWS Console, browser, and AWS CLI verification commands.**

100 Days. 100 Challenges. One Cloud Journey. 

I will continue documenting each AWS challenge with implementation steps, verification, and lessons learned.
