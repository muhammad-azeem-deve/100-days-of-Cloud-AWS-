# Day 43 - Amazon EKS Cluster Commands

This file contains the AWS CLI commands used to verify the resources created during **Day 43 of my 100 Days of Cloud (AWS) Challenge by KodeKloud**.

The challenge was to create an Amazon EKS cluster named `datacenter-eks` in the `us-east-1` region.

---

# 1. Check AWS CLI Version

```bash
aws --version
```

---

# 2. Verify AWS Identity

```bash
aws sts get-caller-identity
```

This command verifies the currently configured AWS account and IAM identity.

---

# 3. Verify the IAM Role

To check whether the required IAM role exists:

```bash
aws iam get-role \
    --role-name eksClusterRole
```

---

# 4. Verify the IAM Trust Relationship

```bash
aws iam get-role \
    --role-name eksClusterRole \
    --query 'Role.AssumeRolePolicyDocument' \
    --output json
```

The trust relationship should allow the EKS service:

```text
eks.amazonaws.com
```

---

# 5. Verify the Attached IAM Policy

```bash
aws iam list-attached-role-policies \
    --role-name eksClusterRole
```

The attached policy should include:

```text
AmazonEKSClusterPolicy
```

---

# 6. List EKS Clusters

To list all EKS clusters in the required region:

```bash
aws eks list-clusters \
    --region us-east-1
```

Expected cluster:

```text
datacenter-eks
```

---

# 7. Verify EKS Cluster Details

```bash
aws eks describe-cluster \
    --name datacenter-eks \
    --region us-east-1
```

This command displays the complete configuration of the cluster.

---

# 8. Verify Cluster Name, Version, and Status

```bash
aws eks describe-cluster \
    --name datacenter-eks \
    --region us-east-1 \
    --query 'cluster.[name,version,status]' \
    --output table
```

Expected information:

```text
-------------------------------------
|          DescribeCluster          |
+----------------+-------+----------+
| datacenter-eks | 1.37  | ACTIVE   |
+----------------+-------+----------+
```

---

# 9. Verify Cluster IAM Role

```bash
aws eks describe-cluster \
    --name datacenter-eks \
    --region us-east-1 \
    --query 'cluster.roleArn' \
    --output text
```

The returned ARN should belong to:

```text
eksClusterRole
```

---

# 10. Verify Kubernetes Endpoint Access

```bash
aws eks describe-cluster \
    --name datacenter-eks \
    --region us-east-1 \
    --query 'cluster.resourcesVpcConfig.[endpointPrivateAccess,endpointPublicAccess]' \
    --output table
```

Expected configuration:

```text
Private Endpoint: True
Public Endpoint:  False
```

---

# 11. Verify EKS Auto Mode

```bash
aws eks describe-cluster \
    --name datacenter-eks \
    --region us-east-1 \
    --query 'cluster.computeConfig'
```

Expected result:

```json
null
```

This confirms that EKS Auto Mode is disabled.

---

# 12. Verify VPC and Subnets

```bash
aws eks describe-cluster \
    --name datacenter-eks \
    --region us-east-1 \
    --query 'cluster.resourcesVpcConfig.[vpcId,subnetIds]' \
    --output json
```

This displays the VPC ID and subnet IDs associated with the EKS cluster.

---

# 13. Verify Availability Zones

First, retrieve the subnet IDs:

```bash
aws eks describe-cluster \
    --name datacenter-eks \
    --region us-east-1 \
    --query 'cluster.resourcesVpcConfig.subnetIds' \
    --output text
```

Then inspect the Availability Zones of those subnets:

```bash
aws ec2 describe-subnets \
    --region us-east-1 \
    --subnet-ids <subnet-id-1> <subnet-id-2> <subnet-id-3> \
    --query 'Subnets[*].[SubnetId,VpcId,AvailabilityZone]' \
    --output table
```

The expected Availability Zones are:

```text
us-east-1a
us-east-1b
us-east-1c
```

---

# 14. Display Important Cluster Configuration Together

```bash
aws eks describe-cluster \
    --name datacenter-eks \
    --region us-east-1 \
    --query 'cluster.{Name:name,Version:version,Status:status,Role:roleArn,EndpointPrivate:endpointPrivateAccess,EndpointPublic:endpointPublicAccess,VPC:vpcConfig.vpcId,Subnets:vpcConfig.subnetIds}' \
    --output json
```

---

# Important AWS CLI Notes

- Always specify the correct AWS region.
- Use `aws eks describe-cluster` to inspect cluster configuration.
- Use `aws iam get-role` to inspect IAM roles.
- Use `aws iam list-attached-role-policies` to verify attached policies.
- Use `aws ec2 describe-subnets` to inspect subnet Availability Zones.
- Keep AWS credentials and sensitive configuration out of GitHub.

---

# Day 43 Final Result

```text
EKS Cluster Name : datacenter-eks
AWS Region       : us-east-1
Kubernetes       : 1.37
IAM Role         : eksClusterRole
Auto Mode        : Disabled
Private Access   : Enabled
Public Access    : Disabled
VPC              : Default VPC
Subnets          : 3
Availability     : us-east-1a, us-east-1b, us-east-1c
Status           : ACTIVE
```

**Day 43 — Completed Successfully ✅**

100 Days. 100 Challenges. One Cloud Journey. ☁️🚀
