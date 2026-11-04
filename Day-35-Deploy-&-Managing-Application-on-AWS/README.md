# Day 35 - Deploying and Managing Applications on AWS

## Challenge Overview

This is Day 35 of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The challenge was related to **Deploying and Managing Applications on AWS using Amazon EC2 and Amazon RDS**.

The Nautilus DevOps team needed a private RDS instance for their application. They wanted to configure a MySQL database and establish a secure connection between an existing EC2 instance and the RDS database.

The task was to deploy a PHP application on the EC2 instance and configure it to communicate with the private MySQL database hosted on Amazon RDS.

### Challenge Requirement

The requirements were:

1. Use the existing EC2 instance named `nautilus-ec2`.
2. Configure its security group to allow HTTP traffic on port `80`.
3. Configure SSH access from the AWS client machine.
4. Create a private RDS instance named `nautilus-rds`.
5. Select MySQL version `8.4.5`.
6. Use the `db.t3.micro` instance class.
7. Set the master username to `nautilus_admin`.
8. Configure RDS storage type as `gp2` with `5 GiB` storage.
9. Create an initial database named `nautilus_db`.
10. Allow the EC2 instance to connect to RDS on port `3306`.
11. Copy the existing `index.php` file from the AWS client to the EC2 instance.
12. Configure the PHP application to connect to the RDS database.
13. Access the EC2 instance through its public IP and verify the following message:

```text
Connected successfully
```

---

## Objectives

The objectives of this challenge are:

- Understand how Amazon RDS works.
- Create and configure a private MySQL database.
- Deploy a PHP application on an EC2 instance.
- Understand EC2 and RDS connectivity.
- Configure security groups and inbound rules.
- Establish password-less SSH access.
- Configure PHP database connectivity.
- Understand database endpoints.
- Verify application deployment through a web browser.
- Verify AWS resources using AWS CLI commands.

---

## Environment

| Item | Details |
| ----------------- | ------------------------------ |
| Challenge | 100 Days of Cloud (AWS) |
| Platform | KodeKloud |
| Day | 35 |
| Cloud Provider | Amazon Web Services |
| Region | `us-east-1` |
| EC2 Instance | `nautilus-ec2` |
| Database Service | Amazon RDS |
| RDS Instance | `nautilus-rds` |
| Database Engine | MySQL |
| MySQL Version | `8.4.5` |
| DB Instance Class | `db.t3.micro` |
| Master Username | `nautilus_admin` |
| Database Name | `nautilus_db` |
| Storage Type | `gp2` |
| Storage Size | `5 GiB` |
| Application | PHP |
| Web Server | Apache |
| HTTP Port | `80` |
| MySQL Port | `3306` |
| Status | Completed |

---

# Solution

## Step 1: Access the AWS Management Console

First, I logged in to the AWS Management Console using the credentials provided by the KodeKloud lab environment.

I selected the required AWS region:

```text
us-east-1
```

All resources for this challenge were created in the specified region.

---

## Step 2: Configure the EC2 Security Group

First, I opened the EC2 dashboard.

Navigation:

```text
AWS Console
    |
    v
    EC2
    |
    v
Network & Security
    |
    v
Security Groups
```

I located the security group associated with the existing EC2 instance named:

```text
nautilus-ec2
```

I selected the security group and opened the **Inbound rules** section.

I selected **Edit inbound rules** and configured the required rules.

### Required Inbound Rules

| Type | Protocol | Port | Source |
|---|---|---|---|
| HTTP | TCP | 80 | `0.0.0.0/0` |
| MySQL/Aurora | TCP | 3306 | EC2 security group |

The HTTP rule allows users to access the PHP application through the EC2 public IP.

The MySQL rule allows the EC2 instance to communicate with the RDS database.

**Important:** The MySQL inbound rule should reference the security group attached to `nautilus-ec2`, rather than opening port 3306 to everyone.

If SSH access is needed, the appropriate SSH rule should also allow access from the AWS client.

After configuring the rules, I saved the changes.

---

## Step 3: Generate an SSH Key on the AWS Client

The challenge required password-less SSH access from the AWS client machine to the EC2 instance.

I opened the AWS client terminal.

I checked whether an SSH key already existed:

```bash
ls -l /root/.ssh/id_rsa*
```

If the key did not exist, I generated a new RSA key pair:

```bash
ssh-keygen -t rsa
```

I used the default location:

```text
/root/.ssh/id_rsa
```

The generated files were:

```text
/root/.ssh/id_rsa
/root/.ssh/id_rsa.pub
```

The private key remained on the AWS client machine.

---

## Step 4: Connect to the EC2 Instance

I opened the EC2 dashboard and selected the existing instance:

```text
nautilus-ec2
```

I selected:

```text
Connect
    |
    v
EC2 Instance Connect
    |
    v
Connect
```

This opened a terminal session directly to the EC2 instance through the AWS Console.

---

## Step 5: Configure Password-less SSH Access

After connecting to the EC2 instance, I switched to the root user:

```bash
sudo su -
```

I moved to the root user's home directory:

```bash
cd ~
```

I opened the SSH authorized keys file:

```bash
nano .ssh/authorized_keys
```

I copied the public key from the AWS client machine:

```text
/root/.ssh/id_rsa.pub
```

I added its contents as a new line in the EC2 instance's:

```text
/root/.ssh/authorized_keys
```

I saved the changes.

This allowed the AWS client to authenticate using its private key without requiring a password.

---

## Step 6: Verify SSH Connectivity

Back on the AWS client machine, I connected to the EC2 instance as root:

```bash
ssh root@<nautilus-ec2-public-ip>
```

I used the actual public IP address of the EC2 instance.

After successful authentication, I verified that I could access the EC2 instance.

---

## Step 7: Create a Private RDS Instance

Next, I created the MySQL database using the AWS Management Console.

Navigation:

```text
AWS Console
    |
    v
Amazon RDS
    |
    v
Databases
    |
    v
Create database
```

I selected the database creation method and configured the required settings.

### Database Creation Configuration

**1. Database Creation Method**

I selected the appropriate database creation method available in the lab.

**2. Engine Options**

I selected:

```text
Engine Type: MySQL
Engine Version: 8.4.5
```

**3. Templates**

I selected the `Sandbox` template provided by the lab.

**4. Availability and Durability**

I selected:

```text
Single-AZ DB instance deployment
```

**5. Settings**

I configured the database settings:

```text
DB Instance Identifier: nautilus-rds
Master Username: nautilus_admin
Master Password: <chosen-secure-password>
```

I used a secure password and kept it private.

**6. Instance Configuration**

I selected:

```text
Instance Class: db.t3.micro
```

**7. Storage Configuration**

I configured:

```text
Storage Type: gp2
Allocated Storage: 5 GiB
```

**8. Connectivity**

I configured the database as private and selected the existing EC2 compute resource:

```text
Compute Resource: Connect to an EC2 compute resource
EC2 Instance: nautilus-ec2
```

I kept the remaining connectivity settings at their appropriate defaults and ensured that the database was not publicly accessible.

**9. Additional Configuration**

Under Database Options, I entered:

```text
Initial Database Name: nautilus_db
```

Finally, I selected:

```text
Create database
```

---

## Step 8: Wait for RDS Availability

After submitting the database configuration, I returned to the RDS Databases dashboard.

I selected:

```text
nautilus-rds
```

I waited until the database status changed to:

```text
Available
```

I also checked the Connectivity & Security section to obtain the database endpoint.

The endpoint is required by the PHP application to connect to the MySQL database.

Example endpoint format:

```text
nautilus-rds.xxxxxxxxxxxx.us-east-1.rds.amazonaws.com
```

I used the actual endpoint generated by AWS, not the example above.

---

## Step 9: Configure the PHP Application

The challenge provided an existing PHP file named:

```text
/root/index.php
```

on the AWS client machine.

I configured the PHP file with the RDS connection information.

The application configuration was:

```php
<?php

$dbname = 'nautilus_db';
$dbuser = 'nautilus_admin';
$dbpass = '<chosen-secure-password>';
$dbhost = '<actual-rds-endpoint>';

$link = mysqli_connect($dbhost, $dbuser, $dbpass)
    or die("Unable to Connect to '$dbhost'");

mysqli_select_db($link, $dbname)
    or die("Could not open the db '$dbname'");

$test_query = "SHOW TABLES FROM $dbname";
$result = mysqli_query($link, $test_query);

$tblCnt = 0;

while ($tbl = mysqli_fetch_array($result)) {
    $tblCnt++;
}

if (!$tblCnt) {
    echo "Connected successfully<br />\n";
} else {
    echo "Connected successfully<br />\n";
}

?>
```

I made sure that:

- The database name matched `nautilus_db`.
- The username matched `nautilus_admin`.
- The password matched the password configured in RDS.
- The database host contained the actual RDS endpoint.

---

## Step 10: Copy index.php to the EC2 Instance

After updating the PHP application, I copied the file from the AWS client to the EC2 instance.

The destination directory was:

```text
/var/www/html/
```

I used SCP from the AWS client:

```bash
scp /root/index.php root@<nautilus-ec2-public-ip>:/var/www/html/
```

This transferred the PHP application to the Apache web server directory.

---

## Step 11: Configure File Permissions

After copying the file, I connected to the EC2 instance and configured the file ownership:

```bash
sudo chown www-data:www-data /var/www/html/index.php
```

I configured the appropriate file permissions:

```bash
sudo chmod 644 /var/www/html/index.php
```

These permissions allow Apache to read the PHP application.

---

## Step 12: Remove the Default Apache Welcome Page

I removed the default Apache welcome page:

```bash
sudo rm /var/www/html/index.html
```

This ensured that the application entry point was the PHP file.

---

## Step 13: Restart Apache

After configuring the PHP application, I restarted the Apache service:

```bash
sudo systemctl restart apache2
```

This ensured that the web server was running with the updated application configuration.

---

## Step 14: Verify the Application in the Browser

Finally, I opened a web browser and entered the public IP address of the EC2 instance:

```text
http://<nautilus-ec2-public-ip>
```

The application connected to the private RDS MySQL database.

The expected browser output was:

```text
Connected successfully
```

This confirmed that:

- The EC2 instance was accessible through HTTP.
- Apache was serving the PHP application.
- PHP was able to connect to the MySQL database.
- The private RDS instance was reachable from EC2.
- The database connection credentials were correct.

---

# Architecture Workflow

The overall architecture of this challenge was:

```text
             Internet
                 |
                 |
                 v
          Public EC2 Instance
             nautilus-ec2
                 |
                 |
           HTTP Port 80
                 |
                 v
          Apache Web Server
                 |
                 |
              PHP App
             index.php
                 |
                 |
          MySQL Port 3306
                 |
                 v
           Private RDS
            nautilus-rds
                 |
                 |
             MySQL 8.4.5
                 |
                 v
             nautilus_db
```

The EC2 instance hosted the PHP application, while Amazon RDS provided the private MySQL database backend.

---

# What I Learned

From this challenge, I learned and practiced:

- How to deploy a PHP application on an EC2 instance.
- How to create a private Amazon RDS database.
- How to configure MySQL 8.4.5 on AWS.
- How to connect EC2 with Amazon RDS.
- How to configure security groups for application and database communication.
- How to generate SSH keys.
- How to configure password-less SSH authentication.
- How to transfer application files using SCP.
- How to configure Apache file permissions.
- How to configure PHP database connectivity using MySQLi.
- How to use an RDS endpoint for database connections.
- How to verify application connectivity through a web browser.
- How to verify AWS resources using AWS CLI.

# Challenge Status

Day 35 — Completed Successfully ✅

**Created the private `nautilus-rds` MySQL database, configured connectivity with the existing `nautilus-ec2` instance, deployed the PHP application, and verified the `Connected successfully` message in the browser.**

100 Days. 100 Challenges. One Cloud Journey. ☁️🚀

I will continue documenting each challenge and building my practical AWS cloud skills step by step.
