# Day 35 - AWS Application Deployment Verification Commands

This file contains **only verification commands** used for Day 35 of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

All AWS resources were created and configured through the AWS Management Console. The following commands are used to verify the EC2 instance, RDS database, security groups, and deployed application.

**AWS Region:** `us-east-1`

---

# 1. Verify AWS Account Identity

To verify the AWS account currently connected to the CLI:

```bash
aws sts get-caller-identity
```

---

# 2. Verify EC2 Instance

To check the status of the EC2 instance:

```bash
aws ec2 describe-instances \
    --filters "Name=tag:Name,Values=nautilus-ec2" \
    --region us-east-1 \
    --query 'Reservations[*].Instances[*].[InstanceId,State.Name,PublicIpAddress,InstanceType]' \
    --output table
```

Expected information:

```text
Instance Name : nautilus-ec2
State         : running
```

To verify its public IP address:

```bash
aws ec2 describe-instances \
    --filters "Name=tag:Name,Values=nautilus-ec2" \
    --region us-east-1 \
    --query 'Reservations[*].Instances[*].PublicIpAddress' \
    --output text
```

---

# 3. Verify RDS Instance Status

To check whether the RDS instance was created successfully:

```bash
aws rds describe-db-instances \
    --db-instance-identifier nautilus-rds \
    --region us-east-1 \
    --query 'DBInstances[0].DBInstanceStatus' \
    --output text
```

Expected output:

```text
available
```

---

# 4. Verify RDS Configuration

To verify the RDS instance configuration:

```bash
aws rds describe-db-instances \
    --db-instance-identifier nautilus-rds \
    --region us-east-1 \
    --query 'DBInstances[0].[DBInstanceIdentifier,Engine,EngineVersion,DBInstanceClass,AllocatedStorage,StorageType,MasterUsername,DBName,PubliclyAccessible]' \
    --output table
```

Expected configuration:

```text
DB Instance       : nautilus-rds
Engine            : mysql
Engine Version    : 8.4.5
Instance Class    : db.t3.micro
Storage           : 5 GiB
Storage Type      : gp2
Master Username   : nautilus_admin
Database Name     : nautilus_db
Public Access     : False
```

---

# 5. Verify RDS Endpoint

To retrieve the RDS endpoint:

```bash
aws rds describe-db-instances \
    --db-instance-identifier nautilus-rds \
    --region us-east-1 \
    --query 'DBInstances[0].Endpoint.Address' \
    --output text
```

To retrieve the database port:

```bash
aws rds describe-db-instances \
    --db-instance-identifier nautilus-rds \
    --region us-east-1 \
    --query 'DBInstances[0].Endpoint.Port' \
    --output text
```

Expected port:

```text
3306
```

---

# 6. Verify EC2 Security Group

First, retrieve the security group attached to the EC2 instance:

```bash
aws ec2 describe-instances \
    --filters "Name=tag:Name,Values=nautilus-ec2" \
    --region us-east-1 \
    --query 'Reservations[*].Instances[*].SecurityGroups[*].[GroupId,GroupName]' \
    --output table
```

After retrieving the security group ID, verify its inbound rules:

```bash
aws ec2 describe-security-groups \
    --group-ids <EC2_SECURITY_GROUP_ID> \
    --region us-east-1 \
    --query 'SecurityGroups[0].IpPermissions' \
    --output json
```

Verify that the required rules include:

```text
HTTP  : TCP 80
MySQL : TCP 3306
```

The MySQL rule should reference the EC2 security group as its source in the RDS security group.

---

# 7. Verify RDS Security Group

To retrieve the security group associated with the RDS instance:

```bash
aws rds describe-db-instances \
    --db-instance-identifier nautilus-rds \
    --region us-east-1 \
    --query 'DBInstances[0].VpcSecurityGroups[*].VpcSecurityGroupId' \
    --output text
```

Use the returned security group ID to verify its inbound rules:

```bash
aws ec2 describe-security-groups \
    --group-ids <RDS_SECURITY_GROUP_ID> \
    --region us-east-1 \
    --query 'SecurityGroups[0].IpPermissions' \
    --output json
```

Verify that MySQL port `3306` allows traffic from the EC2 instance's security group.

---

# 8. Verify Apache Service

After connecting to the EC2 instance, verify the Apache service:

```bash
sudo systemctl status apache2
```

To check only the service status:

```bash
sudo systemctl is-active apache2
```

Expected output:

```text
active
```

---

# 9. Verify PHP Installation

To verify the installed PHP version:

```bash
php -v
```

To verify the MySQLi extension:

```bash
php -m | grep mysqli
```

Expected output:

```text
mysqli
```

---

# 10. Verify Application File

To verify that the PHP application exists in the Apache document root:

```bash
ls -l /var/www/html/index.php
```

To verify the file ownership and permissions:

```bash
stat /var/www/html/index.php
```

---

# 11. Verify HTTP Application Response

To test the application using the EC2 public IP:

```bash
curl -i http://<EC2_PUBLIC_IP>
```

To check only the application response:

```bash
curl -s http://<EC2_PUBLIC_IP>
```

Expected application output:

```text
Connected successfully
```

---

# 12. Final Verification

To verify the RDS instance status:

```bash
aws rds describe-db-instances \
    --db-instance-identifier nautilus-rds \
    --region us-east-1 \
    --query 'DBInstances[0].DBInstanceStatus' \
    --output text
```

To verify the EC2 instance state:

```bash
aws ec2 describe-instances \
    --filters "Name=tag:Name,Values=nautilus-ec2" \
    --region us-east-1 \
    --query 'Reservations[*].Instances[*].State.Name' \
    --output text
```

To verify the application response:

```bash
curl -s http://<EC2_PUBLIC_IP>
```

---

# Day 35 Final Result

```text
EC2 Instance    : nautilus-ec2
EC2 Status      : Running

RDS Instance    : nautilus-rds
RDS Status      : Available

Database Engine : MySQL 8.4.5
Database Name   : nautilus_db
Database Port   : 3306

Application     : PHP
Web Server      : Apache
HTTP Port       : 80

Application     : Connected successfully

Status          : Completed Successfully
```

**Day 35 — Completed Successfully ✅**
