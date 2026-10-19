# Day 26 - EC2 Nginx Web Server Verification Commands

This file contains only the verification commands for Day 26 of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The task was to configure an EC2 instance as an Nginx web server using EC2 User Data.

**AWS Region:** `us-east-1`

---

## 1. Verify AWS CLI Availability

```bash
aws --version
```

---

## 2. Verify AWS Account Identity

```bash
aws sts get-caller-identity
```

---

## 3. Verify EC2 Instance State

```bash
aws ec2 describe-instances \
    --instance-ids <instance-id> \
    --region us-east-1 \
    --query 'Reservations[0].Instances[0].State.Name' \
    --output text
```

Expected result:

```text
running
```

---

## 4. Verify Instance Details

```bash
aws ec2 describe-instances \
    --instance-ids <instance-id> \
    --region us-east-1 \
    --query 'Reservations[0].Instances[0].[InstanceId,InstanceType,State.Name,PublicIpAddress,Placement.AvailabilityZone]' \
    --output table
```

Verify that the instance is running and has a public IP address if public access is required.

---

## 5. Verify EC2 Status Checks

```bash
aws ec2 describe-instance-status \
    --instance-ids <instance-id> \
    --region us-east-1 \
    --include-all-instances \
    --query 'InstanceStatuses[0].[InstanceStatus.Status,SystemStatus.Status]' \
    --output table
```

Expected result after successful initialization:

```text
instance status : ok
system status   : ok
```

---

## 6. Verify the Attached Security Group

```bash
aws ec2 describe-instances \
    --instance-ids <instance-id> \
    --region us-east-1 \
    --query 'Reservations[0].Instances[0].SecurityGroups[*].[GroupId,GroupName]' \
    --output table
```

---

## 7. Verify HTTP Port 80 Inbound Rule

```bash
aws ec2 describe-instances \
    --instance-ids <instance-id> \
    --region us-east-1 \
    --query 'Reservations[0].Instances[0].SecurityGroups[*].GroupId' \
    --output text
```

Use the returned security group ID in the next command:

```bash
aws ec2 describe-security-groups \
    --group-ids <security-group-id> \
    --region us-east-1 \
    --query 'SecurityGroups[*].IpPermissions[*].[IpProtocol,FromPort,ToPort,IpRanges[*].CidrIp]' \
    --output json
```

Verify that TCP port `80` is allowed from the intended source.

---

## 8. Verify Nginx HTTP Response

Use the public IPv4 address returned by the instance details command:

```bash
curl -I --connect-timeout 10 http://<public-ip>
```

A successful response commonly includes:

```text
HTTP/1.1 200 OK
Server: nginx
```

The exact response headers may vary.

---

## 9. Verify the Web Page Content

```bash
curl --connect-timeout 10 http://<public-ip>
```

The default Nginx page should contain welcome-page HTML when the default page is still configured.

---

## 10. Optional: Verify Nginx Directly on the Instance

If SSH access is configured and permitted, connect to the instance using the appropriate username and private key.

For Amazon Linux:

```bash
ssh -i <private-key.pem> ec2-user@<public-ip>
```

Check whether Nginx is active:

```bash
sudo systemctl is-active nginx
```

Expected result:

```text
active
```

Check whether Nginx is enabled at boot:

```bash
sudo systemctl is-enabled nginx
```

Expected result:

```text
enabled
```

Check the installed Nginx version:

```bash
nginx -v
```

Check the HTTP listener:

```bash
sudo ss -lntp | grep ':80'
```

---

## 11. Optional: Verify User Data Execution Logs

If SSH access is available, inspect the cloud-init log:

```bash
sudo cat /var/log/cloud-init-output.log
```

Check the cloud-init completion status:

```bash
sudo cloud-init status --long
```

These commands help identify whether the startup script completed successfully.

---

# Final Verification Checklist

- [ ] AWS CLI is configured for the lab account.
- [ ] EC2 instance is running.
- [ ] EC2 status checks have passed.
- [ ] Public IPv4 address is available.
- [ ] Security group allows the required HTTP traffic.
- [ ] HTTP request returns a successful response.
- [ ] Nginx service is active, if checked over SSH.
- [ ] User Data logs show no relevant installation errors.

**Day 26 — EC2 Nginx Web Server Verification**
