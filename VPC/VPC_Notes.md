# Amazon VPC (Virtual Private Cloud)

## What is Amazon VPC?

Amazon Virtual Private Cloud (VPC) is a logically isolated virtual network within AWS where you can launch and manage AWS resources such as EC2 instances, RDS databases, Elastic Load Balancers, Lambda functions (inside a VPC), and EKS clusters.

It gives you complete control over:

- IP Addressing
- Subnets
- Routing
- Internet Connectivity
- Security
- DNS
- Network Monitoring

Think of a VPC as your own private data center inside AWS.

---

# Why Do We Need a VPC?

Without a VPC, every AWS resource would exist on a shared network with limited control over connectivity and security.

A VPC allows you to:

- Isolate workloads
- Build secure architectures
- Control inbound and outbound traffic
- Design highly available networks
- Connect AWS to on-premises infrastructure
- Build production-ready applications

---

# Default VPC vs Custom VPC

| Default VPC | Custom VPC |
|-------------|------------|
| Created automatically | Created manually |
| Ready to use | Fully customizable |
| Public subnets by default | Public and private subnets as required |
| Internet access configured | You configure networking |
| Suitable for learning | Recommended for production |

**Production environments should always use a Custom VPC.**

---

# Components of a VPC

A VPC is made up of multiple networking components working together.

```
AWS Region
    │
VPC
    │
├── Public Subnet
│      │
│      ├── Internet Gateway
│      └── EC2 (Web Server)
│
└── Private Subnet
       │
       ├── NAT Gateway
       ├── Application Server
       └── Database
```

---

# CIDR Block

A CIDR (Classless Inter-Domain Routing) block defines the IP address range available inside a VPC.

Example:

```
10.0.0.0/16
```

Meaning:

- Network Address → 10.0.0.0
- Total IPs → 65,536

Common CIDR Blocks

| CIDR | Total IPs |
|-------|----------:|
| /16 | 65,536 |
| /20 | 4,096 |
| /24 | 256 |
| /28 | 16 |

AWS reserves **5 IP addresses** in every subnet.

---

# Subnets

A subnet divides a VPC into smaller networks.

Types:

## Public Subnet

Has a route to an Internet Gateway.

Used for:

- Bastion Host
- Load Balancer
- Web Server

---

## Private Subnet

No direct route to the Internet.

Used for:

- Databases
- Backend APIs
- Application Servers
- Internal Services

Private resources can access the internet through a NAT Gateway.

---

# Route Table

A Route Table determines where network traffic should go.

Example

| Destination | Target |
|-------------|--------|
| 10.0.0.0/16 | Local |
| 0.0.0.0/0 | Internet Gateway |

Private subnet example

| Destination | Target |
|-------------|--------|
| 10.0.0.0/16 | Local |
| 0.0.0.0/0 | NAT Gateway |

---

# Internet Gateway (IGW)

An Internet Gateway enables communication between resources inside a VPC and the public internet.

Requirements:

- Attached to VPC
- Public subnet route points to IGW
- EC2 has Public IP or Elastic IP

Without an IGW, internet access is not possible.

---

# NAT Gateway

A NAT Gateway allows resources inside private subnets to access the internet **without exposing them to inbound internet traffic**.

Common uses:

- OS Updates
- Package Downloads
- Docker Image Pulls
- API Calls

NAT Gateway requires:

- Public Subnet
- Elastic IP

---

# Security Groups

Security Groups act as virtual firewalls for EC2 instances.

Characteristics:

- Stateful
- Instance Level
- Allow rules only
- Return traffic automatically allowed

Example

| Port | Purpose |
|------|---------|
| 22 | SSH |
| 80 | HTTP |
| 443 | HTTPS |

---

# Network ACL (NACL)

Network ACLs provide subnet-level security.

Characteristics:

- Stateless
- Allow and Deny rules
- Applied to entire subnet
- Rules evaluated in order

---

# Security Groups vs Network ACL

| Security Group | Network ACL |
|---------------|-------------|
| Stateful | Stateless |
| Instance Level | Subnet Level |
| Allow Rules Only | Allow & Deny |
| Default Allow Outbound | Rule Based |

---

# Elastic IP

An Elastic IP is a static public IPv4 address.

Advantages:

- Remains the same after restarting an instance
- Useful for production servers
- Can be remapped to another instance

---

# VPC Flow Logs

Flow Logs capture information about network traffic.

Useful for:

- Troubleshooting
- Security Auditing
- Network Monitoring
- Compliance

Captured Information:

- Source IP
- Destination IP
- Port
- Protocol
- Action (ACCEPT / REJECT)

---

# VPC Peering

Allows private communication between two VPCs.

Requirements:

- Non-overlapping CIDR blocks
- Route Table updates
- Security Group configuration

Limitations:

- No transitive routing

---

# Transit Gateway

Transit Gateway connects multiple VPCs and on-premises networks through a central hub.

Benefits:

- Simplifies network management
- Scales easily
- Supports hybrid networking

---

# VPN

AWS Site-to-Site VPN securely connects an on-premises network to AWS over the internet using IPSec encryption.

Suitable for:

- Hybrid Cloud
- Disaster Recovery
- Branch Office Connectivity

---

# AWS Direct Connect

AWS Direct Connect provides a dedicated private connection between an organization's data center and AWS.

Advantages:

- Lower latency
- Higher bandwidth
- More consistent performance
- Increased security

---

# DNS in VPC

Amazon VPC provides DNS resolution.

Options include:

- DNS Resolution
- DNS Hostnames
- Route 53 integration

---

# High Availability Best Practices

A production VPC should:

- Span multiple Availability Zones
- Use Public and Private Subnets
- Deploy NAT Gateway per AZ
- Use Load Balancers
- Use Auto Scaling
- Isolate databases
- Enable Flow Logs

---

# Common Production Architecture

```
                Internet
                    │
            Internet Gateway
                    │
          Application Load Balancer
                    │
        ┌──────────────────────────┐
        │     Public Subnets       │
        └──────────────────────────┘
                    │
        ┌──────────────────────────┐
        │   Private App Subnets    │
        └──────────────────────────┘
                    │
        ┌──────────────────────────┐
        │   Private DB Subnets     │
        └──────────────────────────┘
```

---

# Common Interview Questions

### Why use private subnets for databases?

To prevent direct internet access and improve security.

---

### Why use a NAT Gateway?

To allow outbound internet access for private instances while blocking inbound traffic.

---

### Difference between Security Groups and NACLs?

Security Groups are stateful and operate at the instance level, while NACLs are stateless and operate at the subnet level.

---

### Why should production workloads use multiple Availability Zones?

To improve fault tolerance and ensure high availability during infrastructure failures.

---

# Best Practices

- Use Custom VPCs for production.
- Follow the principle of least privilege.
- Keep databases in private subnets.
- Avoid exposing SSH to the internet.
- Use Bastion Hosts or AWS Systems Manager Session Manager for administration.
- Enable VPC Flow Logs for monitoring.
- Plan CIDR blocks carefully to avoid future conflicts.
- Use Multi-AZ architecture for high availability.
- Regularly audit Security Groups and NACLs.
- Remove unused networking resources to reduce complexity.

---

# Key Takeaways

- A VPC is the foundation of networking in AWS.
- Public and private subnets provide network isolation.
- Route Tables determine traffic flow.
- Internet Gateways enable internet connectivity.
- NAT Gateways provide secure outbound internet access for private resources.
- Security Groups and NACLs work together to secure the network.
- A well-designed VPC improves security, scalability, and availability.
