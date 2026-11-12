# Day 43 - Create an Amazon EKS Cluster

## Challenge Overview

This is Day 43 of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The challenge was related to **Amazon Elastic Kubernetes Service (EKS), IAM Roles, Kubernetes Cluster Configuration, and AWS Cloud Infrastructure Management**.

The Nautilus DevOps team needed an Amazon EKS cluster configured securely and ready to support containerized workloads.

The task was to create an EKS cluster named `datacenter-eks` using custom configuration, configure the required IAM role, disable EKS Auto Mode, and enable private endpoint access.

### Challenge Requirement

The requirements were:

1. Access the AWS Management Console.
2. Use the AWS region `us-east-1`.
3. Create an IAM role named `eksClusterRole`.
4. Configure the role to trust the `eks.amazonaws.com` service.
5. Attach the `AmazonEKSClusterPolicy` managed policy.
6. Create an EKS cluster named `datacenter-eks`.
7. Select **Custom configuration**.
8. Use the latest stable Kubernetes version available during the lab.
9. Select `eksClusterRole` as the cluster IAM role.
10. Turn EKS Auto Mode off.
11. Configure cluster endpoint access as **Private**.
12. Use the default VPC with subnets in Availability Zones `a`, `b`, and `c`.
13. Verify that the cluster is successfully created and reaches the `ACTIVE` status.

---

## Objectives

The objectives of this challenge are:

- Understand Amazon EKS and managed Kubernetes services.
- Learn how to create IAM roles for EKS clusters.
- Configure IAM trust relationships.
- Attach AWS managed IAM policies.
- Create an EKS cluster using custom configuration.
- Understand Kubernetes version selection.
- Configure private Kubernetes API endpoint access.
- Understand EKS Auto Mode.
- Configure VPC and subnet networking.
- Verify EKS cluster configuration using AWS CLI.

---

## Environment

| Item | Details |
| ----------------- | ------------------------------ |
| Challenge | 100 Days of Cloud (AWS) |
| Platform | KodeKloud |
| Day | 43 |
| Cloud Provider | Amazon Web Services (AWS) |
| AWS Service | Amazon EKS |
| AWS Region | `us-east-1` |
| Cluster Name | `datacenter-eks` |
| Configuration | Custom |
| Kubernetes Version | `1.37` |
| IAM Role | `eksClusterRole` |
| IAM Policy | `AmazonEKSClusterPolicy` |
| EKS Auto Mode | Disabled |
| Endpoint Access | Private |
| VPC | Default VPC |
| Availability Zones | `us-east-1a`, `us-east-1b`, `us-east-1c` |
| Cluster Status | ACTIVE |
| Challenge Status | Completed |

---

# Solution

## Step 1: Access the AWS Management Console

First, I accessed the AWS Management Console using the temporary credentials provided by the KodeKloud lab environment.

I made sure that the selected AWS region was:

```text
us-east-1
```

All resources for this challenge were created in the specified region.

---

## Step 2: Open IAM

Before creating the EKS cluster, I needed to create the required IAM role.

From the AWS Management Console, I navigated to:

```text
AWS Management Console
        |
        v
       IAM
        |
        v
      Roles
        |
        v
   Create Role
```

---

## Step 3: Create the EKS Cluster IAM Role

I created a new IAM role named:

```text
eksClusterRole
```

For the trusted entity, I selected:

```text
Trusted Entity Type: AWS Service
Service: EKS
Use Case: EKS - Cluster
```

The role was configured to allow the Amazon EKS service to assume it.

The trust relationship uses the following service principal:

```text
eks.amazonaws.com
```

---

## Step 4: Attach the IAM Policy

After configuring the trusted entity, I attached the required AWS managed policy:

```text
AmazonEKSClusterPolicy
```

This policy provides the permissions required by the EKS cluster control plane to manage AWS resources.

I completed the role creation with the following configuration:

```text
Role Name: eksClusterRole

Trusted Service: eks.amazonaws.com

Attached Policy:
AmazonEKSClusterPolicy
```

The IAM role was successfully created.

---

## Step 5: Open Amazon EKS

After creating the IAM role, I opened the Amazon Elastic Kubernetes Service from the AWS Management Console.

The navigation path was:

```text
AWS Management Console
        |
        v
      Amazon EKS
        |
        v
     Clusters
        |
        v
    Create Cluster
```

---

## Step 6: Select Custom Configuration

On the EKS cluster creation page, I selected:

```text
Configuration: Custom configuration
```

This allowed me to configure the cluster settings according to the challenge requirements.

---

## Step 7: Configure Cluster Details

I entered the required cluster information:

```text
Cluster Name: datacenter-eks

Kubernetes Version: 1.37

Cluster IAM Role: eksClusterRole

EKS Auto Mode: Disabled
```

I selected the latest stable Kubernetes version available in the lab environment, which was `1.37`.

I also selected the previously created `eksClusterRole` as the cluster IAM role.

---

## Step 8: Configure Cluster Endpoint Access

The challenge required the Kubernetes API endpoint to be accessible privately.

I configured the endpoint access settings as:

```text
Public Access: Disabled

Private Access: Enabled
```

This ensures that the Kubernetes API endpoint is not publicly accessible over the internet.

The cluster API endpoint can instead be accessed through the appropriate private network connectivity.

---

## Step 9: Configure VPC and Subnets

Next, I configured the networking settings.

The challenge required the default VPC with subnets distributed across three Availability Zones.

I selected:

```text
VPC: Default VPC

Availability Zones:
- us-east-1a
- us-east-1b
- us-east-1c
```

I selected the available subnets associated with these three Availability Zones.

This provided the required networking configuration for the EKS cluster.

---

## Step 10: Review and Create the EKS Cluster

Before creating the cluster, I reviewed the configuration.

The final settings were:

```text
Cluster Name: datacenter-eks

Region: us-east-1

Configuration: Custom

Kubernetes Version: 1.37

IAM Role: eksClusterRole

EKS Auto Mode: Disabled

Endpoint Access: Private

VPC: Default VPC

Subnets: Three subnets

Availability Zones:
us-east-1a
us-east-1b
us-east-1c
```

After verifying the configuration, I selected **Create**.

AWS started provisioning the EKS cluster.

---

## Step 11: Wait for Cluster Creation

EKS cluster creation takes some time because AWS needs to provision and configure the Kubernetes control plane.

I monitored the cluster creation process through the Amazon EKS console.

After the provisioning process finished, the cluster status changed to:

```text
ACTIVE
```

This indicated that the EKS cluster was successfully created.

---


# Final EKS Cluster Configuration

The final configuration of the EKS cluster was:

| Configuration | Final Value |
| ---------------------- | ------------------------------ |
| Cluster Name | `datacenter-eks` |
| Region | `us-east-1` |
| Kubernetes Version | `1.37` |
| IAM Role | `eksClusterRole` |
| IAM Policy | `AmazonEKSClusterPolicy` |
| EKS Auto Mode | Disabled |
| Private Endpoint | Enabled |
| Public Endpoint | Disabled |
| VPC | Default VPC |
| Subnets | 3 |
| Availability Zones | `a`, `b`, `c` |
| Cluster Status | `ACTIVE` |

---

# What I Learned

From this challenge, I learned and practiced:

- How to create an Amazon EKS cluster.
- How Amazon EKS manages Kubernetes control planes.
- How to create IAM roles for EKS.
- How to configure IAM trust relationships.
- How to attach AWS managed policies.
- How to create an EKS cluster using custom configuration.
- How to select a Kubernetes version.
- How to disable EKS Auto Mode.
- How to configure private Kubernetes API endpoint access.
- How to configure VPCs and subnets for EKS.
- How Availability Zones support infrastructure distribution.
- How to verify EKS cluster configuration using AWS CLI.
- How to identify a successfully provisioned EKS cluster using its `ACTIVE` status.

# Important Note

An EKS cluster with an `ACTIVE` status means that the Kubernetes control plane has been successfully provisioned.

However, this does not automatically mean that worker nodes or application workloads are running.

Additional configuration, such as managed node groups or Fargate profiles, may be required before deploying workloads.

Also, because the API endpoint is private, kubectl access requires a suitable network connection to the cluster's VPC.

# Challenge Status

Day 43 — Completed Successfully ✅

**Created the** **`datacenter-eks`** **Amazon EKS cluster using custom configuration, configured the** **`eksClusterRole`** **IAM role, disabled EKS Auto Mode, enabled private endpoint access, selected three subnets across Availability Zones a, b, and c, and verified that the cluster reached the** **`ACTIVE`** **status.**

100 Days. 100 Challenges. One Cloud Journey. ☁️🚀

I will continue documenting each AWS challenge with practical implementation steps, AWS CLI commands, verification, and lessons learned.
