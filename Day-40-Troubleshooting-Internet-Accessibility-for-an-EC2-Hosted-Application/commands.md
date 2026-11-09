# Day 40 - AWS EC2 Internet Accessibility Verification Commands

This file contains only the AWS CLI verification commands for **Day 40** of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The challenge was to troubleshoot an EC2-hosted Nginx application that was inaccessible from the internet.

The root cause was a detached Internet Gateway.

All configuration changes were performed using the AWS Management Console.

**AWS Region:** `us-east-1`

---

# 1. Verify AWS CLI Version

```bash
aws --version
```

---

# 2. Verify AWS Identity

Check the currently authenticated AWS account:

```bash
aws sts get-caller-identity
```

---

# 3. Verify the VPC

Check whether `nautilus-vpc` exists:

```bash
aws ec2 describe-vpcs \
    --filters "Name=tag:Name,Values=nautilus-vpc" \
    --region us-east-1 \
    --query 'Vpcs[*].[VpcId,VpcName,State,CidrBlock]' \
    --output table
```

---

# 4. Verify the EC2 Instance

Check the EC2 instance named `nautilus-ec2`:

```bash
aws ec2 describe-instances \
    --filters "Name=tag:Name,Values=nautilus-ec2" \
    --region us-east-1 \
    --query 'Reservations[*].Instances[*].[InstanceId,State.Name,PublicIpAddress,VpcId,SubnetId]' \
    --output table
```

This command verifies:

- Instance ID
- Instance state
- Public IPv4 address
- VPC ID
- Subnet ID

---

# 5. Verify the Security Group

Check the Security Group named `nautilus-sg`:

```bash
aws ec2 describe-security-groups \
    --filters "Name=tag:Name,Values=nautilus-sg" \
    --region us-east-1 \
    --query 'SecurityGroups[*].[GroupId,GroupName,VpcId]' \
    --output table
```

---

# 6. Verify HTTP Inbound Rules

Check whether the Security Group allows HTTP traffic on port `80`:

```bash
aws ec2 describe-security-groups \
    --filters "Name=tag:Name,Values=nautilus-sg" \
    --region us-east-1 \
    --query 'SecurityGroups[*].IpPermissions[?FromPort==`80` && ToPort==`80`].[IpProtocol,FromPort,ToPort,IpRanges[*].CidrIp]' \
    --output json
```

Expected configuration:

```text
Protocol: TCP
Port:     80
Source:   0.0.0.0/0
```

---

# 7. Verify the Internet Gateway

Check the Internet Gateway named `nautilus-ig`:

```bash
aws ec2 describe-internet-gateways \
    --filters "Name=tag:Name,Values=nautilus-ig" \
    --region us-east-1 \
    --query 'InternetGateways[*].[InternetGatewayId,Attachments[*].VpcId,Attachments[*].State]' \
    --output table
```

After fixing the issue, the expected attachment state is:

```text
attached
```

---

# 8. Verify the Route Table

Check the route table named `nautilus-rtb`:

```bash
aws ec2 describe-route-tables \
    --filters "Name=tag:Name,Values=nautilus-rtb" \
    --region us-east-1 \
    --query 'RouteTables[*].[RouteTableId,VpcId]' \
    --output table
```

---

# 9. Verify the Default Internet Route

Check the default route to the Internet Gateway:

```bash
aws ec2 describe-route-tables \
    --filters "Name=tag:Name,Values=nautilus-rtb" \
    --region us-east-1 \
    --query 'RouteTables[*].Routes[?DestinationCidrBlock==`0.0.0.0/0`].[DestinationCidrBlock,GatewayId,State]' \
    --output table
```

Expected configuration after the fix:

```text
Destination: 0.0.0.0/0
Target:      nautilus-ig
State:       active
```

---

# 10. Verify the Subnet

Check the subnet named `nautilus-subnet`:

```bash
aws ec2 describe-subnets \
    --filters "Name=tag:Name,Values=nautilus-subnet" \
    --region us-east-1 \
    --query 'Subnets[*].[SubnetId,VpcId,CidrBlock,MapPublicIpOnLaunch]' \
    --output table
```

---

# 11. Verify Application Accessibility

First, retrieve the public IPv4 address of the EC2 instance:

```bash
aws ec2 describe-instances \
    --filters "Name=tag:Name,Values=nautilus-ec2" \
    --region us-east-1 \
    --query 'Reservations[*].Instances[*].PublicIpAddress' \
    --output text
```

Then test HTTP connectivity:

```bash
curl -I http://<EC2-PUBLIC-IP>
```

A successful HTTP response confirms that the web server is responding.

You can also check the response body:

```bash
curl http://<EC2-PUBLIC-IP>
```

The Nginx welcome page should be returned.

---

# Final Verification Checklist

| Verification | Expected Result |
|-----------------------------|------------------|
| VPC | `nautilus-vpc` exists |
| EC2 Instance | Running |
| Security Group | HTTP port 80 allowed |
| Internet Gateway | Attached |
| Route Table | `nautilus-rtb` |
| Default Route | `0.0.0.0/0` |
| Route State | Active |
| Application | Nginx accessible |
| HTTP Port | 80 |

---

# Day 40 Result

```text
VPC              : nautilus-vpc
EC2 Instance     : nautilus-ec2
Security Group   : nautilus-sg
Internet Gateway : nautilus-ig
Route Table      : nautilus-rtb
Subnet           : nautilus-subnet

Internet Gateway : Attached
Route State      : Active
Application      : Accessible
HTTP Port        : 80

Status           : Successfully Verified
```

**Day 40 — Completed Successfully ✅**

100 Days. 100 Challenges. One Cloud Journey. ☁️🚀
