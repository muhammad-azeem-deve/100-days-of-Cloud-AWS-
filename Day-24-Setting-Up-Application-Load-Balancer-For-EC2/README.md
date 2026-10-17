# Day 24 - Setting Up an Application Load Balancer for an EC2 Instance

## Challenge Overview

This is Day 24 of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The challenge was related to **AWS Elastic Load Balancing, Application Load Balancers, Target Groups, Security Groups, and EC2 Instance Health Checks**.

The task was to configure an Application Load Balancer (ALB) to forward incoming HTTP traffic to an EC2 instance.

During this task, I created the required security group, configured the target group, allowed HTTP traffic on port 80, and created an Application Load Balancer.

Initially, the load balancer DNS URL was not accessible through a web browser. After troubleshooting, I identified configuration issues involving the security group, target group health status, and an incorrectly selected Availability Zone for the EC2 instance's network configuration.

Correcting these settings helped me understand how the load balancer, target group, EC2 instance, and network configuration work together.

### Challenge Requirements

The requirements were:

1. Access the AWS Management Console.
2. Select the required AWS region: `us-east-1`.
3. Create the security group using the exact name specified in the lab.
4. Configure the security group to allow the required HTTP traffic.
5. Create a target group for the EC2 instance.
6. Register the EC2 instance with the target group.
7. Configure the EC2 security group to allow inbound HTTP traffic on port 80 from the appropriate source.
8. Create an Application Load Balancer.
9. Configure the load balancer's Availability Zones and subnets correctly.
10. Configure an HTTP listener on port 80 and forward requests to the target group.
11. Verify the target group's health status.
12. Access the application using the load balancer's DNS name.



---

## Objectives

The objectives of this challenge are:

- Understand the purpose of an Application Load Balancer.
- Configure an AWS security group.
- Create and configure a target group.
- Register an EC2 instance as a target.
- Configure inbound HTTP traffic on port 80.
- Understand listeners and listener rules.
- Configure Availability Zones and subnets.
- Troubleshoot unhealthy targets.
- Diagnose connectivity problems with an ALB DNS name.
- Verify AWS resources using the AWS Console and AWS CLI.

---

## Environment

| Item | Details |
|---|---|
| Challenge | 100 Days of Cloud (AWS) |
| Platform | KodeKloud |
| Day | 24 |
| Cloud Provider | Amazon Web Services |
| Region | `us-east-1` |
| Load Balancer | Application Load Balancer |
| Target | EC2 Instance |
| Protocol | HTTP |
| Port | `80` |
| Security Group | Lab-specified name |
| Target Group | Lab-specified name |
| Status | Completed |

---

# Solution

## Step 1: Access the AWS Management Console

First, I logged in to the AWS Management Console using the credentials provided by the KodeKloud lab.

I selected the required AWS region:

```text
us-east-1
```

All resources for this challenge were configured in the required region.

---

## Step 2: Create the Security Group

I opened the EC2 service and navigated to **Security Groups**.

I created a security group using the exact name specified by the lab.

The security group was configured to allow the required HTTP traffic.

The intended inbound rule was:

| Type | Protocol | Port | Source |
|---|---|---|---|
| HTTP | TCP | 80 | Appropriate permitted source |

For a public-facing load balancer, the load balancer security group generally allows HTTP traffic from `0.0.0.0/0` when public HTTP access is required by the lab.

For the EC2 instance, the recommended configuration is to allow inbound HTTP traffic from the load balancer's security group instead of allowing HTTP traffic from everyone.

This allows the load balancer to communicate with the instance while limiting direct access.

---

## Step 3: Check the EC2 Instance

I opened the EC2 dashboard and selected the instance specified in the lab.

I checked the following information:

- Instance state was `Running`.
- The instance was in the intended VPC.
- The instance's subnet and Availability Zone were correct.
- The security group allowed the required application traffic.
- The application was expected to respond on port 80.

The EC2 instance must be reachable from the load balancer through the correct network and security group configuration.

---

## Step 4: Create the Target Group

I navigated to:

```text
EC2
  → Target Groups
  → Create target group
```

I selected the target type appropriate for the lab, normally **Instances**, and configured the target group for HTTP traffic on port 80.

I selected the correct VPC and configured the health check settings.

The target group was intended to route traffic to the EC2 instance.

### Target Group Configuration

| Setting | Value |
|---|---|
| Target type | Instances |
| Protocol | HTTP |
| Port | `80` |
| VPC | Same VPC as the EC2 instance |
| Health check protocol | HTTP |
| Health check port | Traffic port |
| Health check path | `/` if the application responds there |

The health check path must return a successful response for the target to become healthy. If the application uses a different path, the configured health check path should match it.

---

## Step 5: Register the EC2 Instance

After creating the target group, I opened its **Targets** tab.

I registered the EC2 instance specified in the lab on port 80.

I then checked the target registration status.

Registering the instance allows the load balancer to forward incoming requests to it.

However, registration alone does not guarantee that the target is healthy. The target group health check must also succeed.

---

## Step 6: Configure the EC2 Security Group

During troubleshooting, I checked whether the EC2 instance's security group permitted inbound traffic on port 80.

The required configuration was:

| Setting | Value |
|---|---|
| Type | HTTP |
| Protocol | TCP |
| Port range | `80` |
| Source | Load balancer security group |

The source should be the security group attached to the Application Load Balancer.

If the instance security group blocks port 80, the load balancer may be unable to communicate with the application, causing health checks to fail.

I also checked that the security group attached to the load balancer permitted the intended incoming HTTP requests.

---

## Step 7: Create the Application Load Balancer

I navigated to:

```text
EC2
  → Load Balancers
  → Create load balancer
  → Application Load Balancer
```

I configured the load balancer using the settings required by the lab.

The main configuration included:

- The required load balancer name.
- An internet-facing scheme, if public browser access was required.
- The correct IP address type.
- The correct VPC.
- Appropriate subnets in the required Availability Zones.
- The security group created for the load balancer.

The load balancer needed to be associated with suitable subnets in the intended Availability Zones.

### Availability Zone Troubleshooting

One of the important issues I encountered was selecting an incorrect Availability Zone during configuration.

An Application Load Balancer must have suitable subnet mappings in the selected Availability Zones, and the registered EC2 target must be reachable through the VPC network.

I revisited the EC2 instance's subnet and Availability Zone and corrected the configuration so that the load balancer's subnet selection was appropriate for the lab environment.

The load balancer's subnet Availability Zones and the instance's Availability Zone do not always need to be identical. What matters is that the ALB has valid subnet mappings and can reach the registered target. For a typical public ALB, at least two Availability Zones are configured.

---

## Step 8: Configure the Listener

I configured an HTTP listener on port 80.

The listener's default action forwarded incoming requests to the target group created earlier.

The traffic flow was:

```text
Browser
   |
   | HTTP request on port 80
   v
Application Load Balancer
   |
   | Listener: HTTP : 80
   v
Target Group
   |
   | HTTP request on port 80
   v
EC2 Instance
   |
   v
Application Response
```

If the listener forwards traffic to the wrong target group, the browser may not display the expected application even when the load balancer itself is active.

---

## Step 9: Verify the Target Group Health Status

After creating the load balancer, I opened the target group's **Targets** tab and checked the health status of the registered EC2 instance.

The important states were:

- `initial` — health checks are still being performed.
- `healthy` — the target is passing health checks.
- `unhealthy` — the target is failing health checks.
- `unused` — the target is not currently being used by the load balancer configuration.
- `draining` — the target is being removed from service.

The expected final state was:

```text
Target health: healthy
```

If the target was unhealthy, I checked the reason reported by AWS and reviewed the application, health check path, target port, security groups, and network configuration.

---

## Step 10: Troubleshoot the Load Balancer DNS URL

Initially, the load balancer DNS URL was not accessible through my web browser.

I did not assume that the DNS name alone was the problem. I checked the network and target configuration step by step.

### Issue 1: Security Group Configuration

I checked the load balancer security group to confirm that inbound HTTP traffic on port 80 was permitted.

I also checked the EC2 security group to confirm that it allowed HTTP traffic from the load balancer security group.

A missing or incorrect inbound rule can prevent the health check or application request from reaching the instance.

### Issue 2: Target Group Health Status

I checked the target group to see whether the EC2 instance was healthy.

If the target is unhealthy, the load balancer may return an error such as `503 Service Unavailable`, even though the DNS name resolves.

I reviewed the health check reason, protocol, port, path, and the instance's application status.

### Issue 3: Incorrect Availability Zone Selection

I found that the Availability Zone selection was another configuration issue.

I reviewed the subnet associated with the EC2 instance and the subnet mappings selected for the load balancer.

I corrected the selection to use valid subnets in the intended Availability Zones.

The ALB does not require every target to be in the same Availability Zone as the load balancer nodes, but its subnet mappings and target network configuration must be valid.

### Issue 4: Listener Configuration

I verified that the load balancer had a listener on port 80 and that its default action forwarded requests to the correct target group.

### Issue 5: Application Availability

I checked whether the application on the EC2 instance was listening on port 80 and returning a response at the configured health check path.

These checks helped me isolate the problem instead of repeatedly recreating resources.

---

## Step 11: Access the Application

After correcting the configuration, I used the DNS name shown on the load balancer's details page.

The URL format was:

```text
http://<load-balancer-dns-name>
```

I opened the URL in a web browser and checked whether the application responded.

The load balancer DNS name is generated by AWS, so the actual hostname must be copied from the EC2 Load Balancers console rather than guessed.

---

## Step 12: Final Console Verification

Before considering the task complete, I checked the following resources in the AWS Console:

| Resource | Verification |
|---|---|
| Security group | Required inbound rules configured |
| EC2 instance | Running in the intended VPC and subnet |
| Target group | Correct EC2 instance registered |
| Target health | Healthy |
| Application Load Balancer | Active |
| Availability Zones | Appropriate subnets selected |
| Listener | HTTP on port 80 |
| Listener action | Forwards to the intended target group |
| DNS URL | Application responds in browser |

---

# What I Learned

From this challenge, I learned and practiced:

- How to create and configure an Application Load Balancer.
- How to create security groups for a load balancer and an EC2 instance.
- How to create a target group and register an EC2 instance.
- How HTTP listeners forward requests to target groups.
- How health checks determine whether an EC2 target can receive traffic.
- How Availability Zones and subnets affect load balancer configuration.
- How security group rules affect communication between AWS resources.
- How to troubleshoot an inaccessible load balancer DNS URL.
- Why an active load balancer does not necessarily mean its targets are healthy.
- How to verify AWS resources using the AWS Management Console and AWS CLI.

# Challenge Status

Day 24 — Completed Successfully ✅

**Configured an Application Load Balancer for an EC2 instance, created the required security group and target group, configured HTTP access on port 80, and troubleshot connectivity issues involving security groups, target health, and Availability Zone selection.**

100 Days. 100 Challenges. One Cloud Journey. ☁️🚀

I will continue documenting each challenge as I build practical AWS cloud and infrastructure skills.
