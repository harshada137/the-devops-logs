# Amazon EC2 (Elastic Compute Cloud)

## Overview

Amazon Elastic Compute Cloud (EC2) is a service provided by AWS that allows you to launch and manage virtual servers in the cloud. These virtual servers are called **EC2 instances**.

Instead of purchasing physical hardware, you can provision compute resources on demand and pay only for what you use.

EC2 is one of the core AWS services and is widely used to host applications, websites, APIs, databases, CI/CD runners, Kubernetes worker nodes, and many other workloads.

---

# Why EC2?

Before cloud computing, organizations had to:

- Purchase physical servers
- Maintain data centers
- Handle hardware failures
- Plan capacity months in advance
- Pay large upfront costs

EC2 solves these challenges by providing virtual machines that can be created or terminated within minutes.

### Advantages

- On-demand provisioning
- Pay-as-you-go pricing
- High availability
- Easy scalability
- Multiple operating systems
- Integration with other AWS services

---

# EC2 Architecture

A typical EC2 deployment consists of several AWS components working together.

```
AWS Region
    │
Availability Zone
    │
   VPC
    │
Subnet
    │
Security Group
    │
EC2 Instance
    │
EBS Volume
```

### Region

A geographical location where AWS maintains data centers.

Example:

- Mumbai
- Singapore
- Frankfurt
- Ohio

---

### Availability Zone (AZ)

An isolated data center within a Region.

Using multiple Availability Zones improves application availability and fault tolerance.

---

### VPC (Virtual Private Cloud)

A logically isolated virtual network where AWS resources are deployed.

It provides complete control over networking.

---

### Subnet

A subnet is a smaller network inside a VPC.

Types:

- Public Subnet
- Private Subnet

---

### Security Group

Acts as a virtual firewall for an EC2 instance.

It controls:

- Inbound traffic
- Outbound traffic

Security Groups are **stateful**, meaning return traffic is automatically allowed.

---

### Network ACL

A firewall applied at the subnet level.

Unlike Security Groups, Network ACLs are **stateless**.

---

# Amazon Machine Image (AMI)

An AMI is a template used to launch an EC2 instance.

It contains:

- Operating System
- Required software
- Configuration
- Root volume

Examples:

- Amazon Linux 2023
- Ubuntu
- Windows Server
- Red Hat Enterprise Linux

---

# EC2 Instance Types

Instance types define the hardware allocated to an EC2 instance.

They determine:

- CPU
- Memory
- Storage performance
- Network performance

Common families:

### T Series

Burstable general-purpose instances.

Suitable for:

- Small websites
- Development
- Testing

---

### M Series

Balanced compute and memory.

Suitable for:

- Web applications
- Backend APIs
- Business applications

---

### C Series

Compute optimized.

Suitable for:

- Batch processing
- High-performance computing
- CI/CD runners

---

### R Series

Memory optimized.

Suitable for:

- Databases
- In-memory caching
- Analytics

---

# Storage Options

## Amazon EBS

Elastic Block Store provides persistent storage.

Features:

- Persistent
- Snapshot support
- Can be resized
- High durability

---

## Instance Store

Temporary storage attached to the physical host.

Data is lost if the instance stops or terminates.

---

# EC2 Lifecycle

The common lifecycle states are:

- Pending
- Running
- Stopping
- Stopped
- Rebooting
- Shutting Down
- Terminated

Understanding these states helps in managing costs and availability.

---

# Pricing Models

## On-Demand

Pay only for the resources you consume.

Best for:

- Development
- Testing
- Short-term workloads

---

## Reserved Instances

Lower cost in exchange for a long-term commitment.

Best for predictable workloads.

---

## Savings Plans

Flexible pricing model that provides discounts based on committed usage.

---

## Spot Instances

Use unused AWS capacity at a significantly lower price.

Suitable for:

- Batch jobs
- Kubernetes worker nodes
- CI/CD pipelines
- Fault-tolerant workloads

---

# Security Best Practices

- Never expose unnecessary ports.
- Use Security Groups with least privilege.
- Use IAM Roles instead of storing AWS credentials.
- Rotate key pairs when required.
- Regularly update the operating system.
- Enable monitoring and logging.

---

# Common EC2 Use Cases

- Hosting websites
- Running backend APIs
- CI/CD runners
- Kubernetes worker nodes
- Bastion hosts
- Monitoring servers
- Development environments
- Testing environments

---

# Key Takeaways

- EC2 provides scalable virtual servers in the AWS Cloud.
- AMIs are templates used to launch instances.
- Instance types determine compute capacity.
- EBS provides persistent storage.
- Security Groups protect instances from unauthorized access.
- Multiple pricing models help optimize costs.
- EC2 integrates with services like IAM, VPC, CloudWatch, Auto Scaling, and Elastic Load Balancing.
