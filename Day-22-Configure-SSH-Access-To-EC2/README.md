# Day 22 - Configure Secure SSH Access to an EC2 Instance

## Challenge Overview

This is Day 22 of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The challenge was related to **AWS EC2 Instance Launch, SSH Key Generation, User Data Configuration, and Secure Remote Access**.

The Nautilus DevOps team required a secure way to access an EC2 instance over SSH. The task involved generating an SSH key pair using the AWS client terminal, launching an EC2 instance with the `t2.micro` instance type, and configuring the instance to authorize the generated public key through a user data script.

The main objective was to configure SSH access without relying on password-based authentication.

### Challenge Requirement

The requirements were:

1. Access the AWS client terminal.
2. Generate an SSH key pair using `ssh-keygen`.
3. Copy the generated public key.
4. Open the AWS Management Console.
5. Launch an EC2 instance using the `t2.micro` instance type.
6. Configure the instance's user data to install the public key into the appropriate user's `authorized_keys` file.
7. Launch the instance and verify its status in the AWS Console.
8. Verify the instance configuration using AWS CLI commands.

---

## Objectives

The objectives of this challenge are:

- Generate an SSH key pair using the terminal.
- Understand the difference between public and private SSH keys.
- Launch an EC2 instance using the AWS Management Console.
- Understand the purpose of EC2 user data.
- Configure public-key authentication for SSH.
- Verify EC2 instance details using the AWS Console.
- Verify instance configuration using AWS CLI.
- Follow security best practices for private keys and SSH access.

---

## Environment

| Item | Details |
|---|---|
| Challenge | 100 Days of Cloud (AWS) |
| Platform | KodeKloud |
| Day | 22 |
| Cloud Provider | Amazon Web Services (AWS) |
| Service | Amazon EC2 |
| Instance Type | `t2.micro` |
| SSH Authentication | Public-key authentication |
| Key Generation Tool | `ssh-keygen` |
| Configuration Method | EC2 User Data |
| Verification | AWS Console and AWS CLI |
| Status | Completed |

---

# Solution

## Step 1: Access the AWS Client Terminal

First, I accessed the AWS client machine provided by the KodeKloud lab.

I used the terminal to generate an SSH key pair before launching the EC2 instance.

I checked whether SSH key-generation tools were available:

```bash
ssh-keygen -h
```

Depending on the installed OpenSSH version, this command may display usage information or an error because `-h` is not supported as a help option. The important requirement is that `ssh-keygen` is installed and available.

---

## Step 2: Generate the SSH Key Pair

I generated an RSA SSH key pair on the AWS client machine:

```bash
ssh-keygen -t rsa -b 2048 -f ~/.ssh/nautilus-ec2-key -N ""
```

This command generated two files:

```text
~/.ssh/nautilus-ec2-key
~/.ssh/nautilus-ec2-key.pub
```

Their purposes are:

- **Private key:** `nautilus-ec2-key` — kept securely on the client machine.
- **Public key:** `nautilus-ec2-key.pub` — added to the EC2 instance to authorize SSH access.

I displayed the public key:

```bash
cat ~/.ssh/nautilus-ec2-key.pub
```

I copied the complete public key output for use in the EC2 user data script.

**Security note:** The private key must never be copied into user data or uploaded to GitHub.

---

## Step 3: Open the AWS Management Console

I opened the AWS Management Console using the credentials provided by KodeKloud.

I selected the required AWS region specified by the lab. If the lab requires `us-east-1`, I selected **US East (N. Virginia)**.

I navigated to:

```text
AWS Management Console
        |
        v
       EC2
        |
        v
    Instances
        |
        v
  Launch instances
```

---

## Step 4: Configure the EC2 Instance

I selected **Launch instances** and configured the instance.

The required setting was:

```text
Instance type: t2.micro
```

I selected a suitable Amazon Linux AMI for the lab and configured the remaining required launch settings.

For networking, I ensured that the instance had an appropriate security group allowing SSH traffic on TCP port 22 from the authorized client IP address.

For a real deployment, SSH should not be exposed to everyone using `0.0.0.0/0` unless there is a specific, justified requirement.

---

## Step 5: Configure User Data

During instance launch, I expanded **Advanced details** and located the **User data** field.

I added a shell script that installs the generated public key into the default Amazon Linux user's SSH authorization file.

Example user data:

```bash
#!/bin/bash
set -e

mkdir -p /home/ec2-user/.ssh
chmod 700 /home/ec2-user/.ssh

cat >> /home/ec2-user/.ssh/authorized_keys <<'EOF'
PASTE_YOUR_GENERATED_PUBLIC_KEY_HERE
EOF

chown -R ec2-user:ec2-user /home/ec2-user/.ssh
chmod 600 /home/ec2-user/.ssh/authorized_keys
```

I replaced `PASTE_YOUR_GENERATED_PUBLIC_KEY_HERE` with the complete public key generated in Step 2.

**Important:** This example assumes an Amazon Linux AMI with the default `ec2-user` account. Ubuntu AMIs normally use `ubuntu`, so the username and home directory must match the selected AMI.

The script performs the following operations:

1. Creates the `.ssh` directory.
2. Sets secure directory permissions.
3. Adds the public key to `authorized_keys`.
4. Assigns ownership to the correct Linux user.
5. Sets the appropriate permissions on `authorized_keys`.

The private key remains on the AWS client machine.

---

## Step 6: Launch the EC2 Instance

After configuring the instance type, networking, and user data, I reviewed the settings and selected **Launch instance**.

I waited for the instance to reach the `Running` state and for its status checks to complete.

The expected configuration was:

```text
Instance Type : t2.micro
Instance State: running
SSH Port      : 22
Authentication: SSH public key
```

---

## Step 7: Verify the Instance in the AWS Console

I opened:

```text
EC2
  -> Instances
  -> Select the launched instance
```

I checked the following information:

- Instance ID
- Instance state
- Instance type
- Public IPv4 address
- Security group
- Availability zone
- Status checks

I also reviewed the instance's launch configuration and user data where available.

The instance needed a reachable network path, an appropriate SSH security group rule, and the correct public key installed by the user data script.

---

## Step 8: Verify SSH Access

After the instance was running and its initialization had completed, I used the private key from the AWS client machine to connect.

For an Amazon Linux instance:

```bash
ssh -i ~/.ssh/nautilus-ec2-key ec2-user@<EC2_PUBLIC_IP>
```

I replaced `<EC2_PUBLIC_IP>` with the actual public IPv4 address displayed in the EC2 Console.

If the connection succeeded, it confirmed that the instance accepted the generated public key.

If the connection failed, I would check:

- Whether the instance was running.
- Whether the status checks had passed.
- Whether the security group allowed TCP port 22 from the client IP.
- Whether the correct public key was present in `authorized_keys`.
- Whether the AMI's default username was correct.
- Whether the private key matched the installed public key.

---

# Verification Using AWS CLI

The AWS CLI was used to verify the instance configuration independently of the AWS Console.

The verification commands are documented in `commands.md`.

The checks included:

- Confirming the active AWS identity.
- Locating the EC2 instance.
- Checking the instance type and state.
- Checking the public IP address.
- Inspecting the security group.
- Inspecting the configured user data.

---

# SSH Authentication Workflow

```text
AWS Client Machine
        |
        v
Generate SSH Key Pair
        |
        +----------------------+
        |                      |
        v                      v
    Private Key            Public Key
        |                      |
        |                      v
        |                EC2 User Data
        |                      |
        |                      v
        |             authorized_keys
        |                      |
        v                      |
SSH Client Connects            |
        |                      |
        +----------+-----------+
                   |
                   v
         Public-Key Authentication
                   |
                   v
            Secure SSH Access
```

---

# What I Learned

From this challenge, I learned and practiced:

- How to generate an RSA SSH key pair.
- The difference between SSH public and private keys.
- How to launch an EC2 instance using the AWS Console.
- How to select the `t2.micro` instance type.
- How EC2 user data scripts run during instance initialization.
- How to install an SSH public key into `authorized_keys`.
- How Linux file permissions affect SSH authentication.
- How security groups control inbound SSH traffic.
- How to verify EC2 resources using AWS CLI.
- Why private keys and credentials must be protected.

# Challenge Status

Day 22 — Completed Successfully 

**Generated an SSH key pair, launched a `t2.micro` EC2 instance, configured the public key through user data, and documented the Console and CLI verification steps for secure SSH access.**

100 Days. 100 Challenges. One Cloud Journey. 

I will continue documenting each challenge to strengthen my practical AWS and cloud infrastructure skills.
