# Amazon VPC (Virtual Private Cloud) — Notes

## 1. What is a VPC?

Amazon Virtual Private Cloud (VPC) is a logically isolated virtual network within AWS where you launch and manage resources — EC2, RDS, ELB, Lambda (VPC-attached), EKS, etc.

It gives you full control over:

- IP addressing
- Subnets
- Routing
- Internet connectivity
- Security
- DNS
- Network monitoring

> Think of a VPC as your own private data center inside AWS.

---

## 2. Why Use a VPC?

Without a VPC, resources sit on a shared network with limited control. A VPC lets you:

- Isolate workloads
- Build secure architectures
- Control inbound/outbound traffic
- Design highly available networks
- Connect AWS to on-prem infrastructure
- Build production-ready applications

---

## 3. Default VPC vs Custom VPC

| Default VPC | Custom VPC |
|---|---|
| Created automatically | Created manually |
| Ready to use | Fully customizable |
| Public subnets by default | Public + private subnets as needed |
| Internet access pre-configured | You configure networking |
| Good for learning | **Recommended for production** |

---

## 4. VPC Components — Overview

```
AWS Region
    │
    VPC
    │
    ├── Public Subnet
    │      ├── Internet Gateway
    │      └── EC2 (Web Server)
    │
    └── Private Subnet
           ├── NAT Gateway
           ├── Application Server
           └── Database
```

---

## 5. CIDR Block

Defines the IP range available inside a VPC.

Example: `10.0.0.0/16` → Network address `10.0.0.0`, 65,536 total IPs.

| CIDR | Total IPs |
|---|---:|
| /16 | 65,536 |
| /20 | 4,096 |
| /24 | 256 |
| /28 | 16 |

> AWS reserves **5 IP addresses** in every subnet.

---

## 6. Subnets

### Public Subnet
Has a route to an Internet Gateway. Used for:
- Bastion host
- Load balancer
- Web server

### Private Subnet
No direct route to the internet. Used for:
- Databases
- Backend APIs
- Application servers
- Internal services

> Private resources reach the internet via a **NAT Gateway**.

---

## 7. Route Tables

**Public subnet:**

| Destination | Target |
|---|---|
| 10.0.0.0/16 | Local |
| 0.0.0.0/0 | Internet Gateway |

**Private subnet:**

| Destination | Target |
|---|---|
| 10.0.0.0/16 | Local |
| 0.0.0.0/0 | NAT Gateway |

---

## 8. Internet Gateway (IGW)

Enables communication between VPC resources and the public internet.

Requirements:
- Attached to the VPC
- Public subnet route points to the IGW
- EC2 has a public IP or Elastic IP

No IGW → no internet access.

---

## 9. NAT Gateway

Lets private-subnet resources reach the internet **outbound-only** (no inbound exposure).

Common uses:
- OS updates
- Package downloads
- Docker image pulls
- API calls

Requirements:
- Deployed in a public subnet
- Needs an Elastic IP

---

## 10. Security Groups

Virtual firewalls at the **instance** level.

- Stateful
- Allow rules only
- Return traffic auto-allowed

| Port | Purpose |
|---|---|
| 22 | SSH |
| 80 | HTTP |
| 443 | HTTPS |

---

## 11. Network ACL (NACL)

Security at the **subnet** level.

- Stateless
- Allow **and** Deny rules
- Rules evaluated in order

---

## 12. Security Groups vs NACL

| Security Group | Network ACL |
|---|---|
| Stateful | Stateless |
| Instance level | Subnet level |
| Allow only | Allow & Deny |
| Default allow outbound | Rule based |

---

## 13. Elastic IP

A static public IPv4 address.

- Persists across instance restarts
- Good for production servers
- Can be remapped to another instance

---

## 14. VPC Flow Logs

Captures network traffic metadata. Useful for troubleshooting, security auditing, monitoring, and compliance.

Captured fields:
- Source IP
- Destination IP
- Port
- Protocol
- Action (ACCEPT / REJECT)

---

## 15. VPC Peering

Private connectivity between two VPCs.

Requirements:
- Non-overlapping CIDR blocks
- Route table updates
- Security group configuration

Limitation: **no transitive routing**.

---

## 16. Transit Gateway

Central hub connecting multiple VPCs and on-prem networks.

Benefits:
- Simplifies network management
- Scales easily
- Supports hybrid networking

---

## 17. Site-to-Site VPN

Connects on-prem networks to AWS over the internet via IPSec encryption.

Good for:
- Hybrid cloud
- Disaster recovery
- Branch office connectivity

---

## 18. AWS Direct Connect

Dedicated private connection between a data center and AWS.

Advantages:
- Lower latency
- Higher bandwidth
- More consistent performance
- Increased security

---

## 19. DNS in VPC

- DNS resolution
- DNS hostnames
- Route 53 integration

---

## 20. High-Availability Best Practices

A production VPC should:
- Span multiple Availability Zones
- Use public + private subnets
- Deploy a NAT Gateway per AZ
- Use load balancers
- Use auto scaling
- Isolate databases
- Enable Flow Logs

---

## 21. Common Production Architecture

```
                Internet
                    │
            Internet Gateway
                    │
          Application Load Balancer
                    │
        ┌──────────────────────────┐
        │      Public Subnets      │
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

## 22. Common Interview Questions

**Why use private subnets for databases?**
To prevent direct internet access and improve security.

**Why use a NAT Gateway?**
To allow outbound internet access for private instances while blocking inbound traffic.

**Security Groups vs NACLs?**
Security Groups are stateful and instance-level; NACLs are stateless and subnet-level.

**Why multi-AZ for production?**
Improves fault tolerance and ensures high availability during infrastructure failures.

---

## 23. Best Practices Checklist

- [ ] Use Custom VPCs for production
- [ ] Follow least-privilege principle
- [ ] Keep databases in private subnets
- [ ] Avoid exposing SSH to the internet
- [ ] Use Bastion Host or AWS Systems Manager Session Manager for admin access
- [ ] Enable VPC Flow Logs
- [ ] Plan CIDR blocks carefully to avoid future conflicts
- [ ] Use Multi-AZ architecture
- [ ] Regularly audit Security Groups and NACLs
- [ ] Remove unused networking resources

---

## 24. Key Takeaways

- A VPC is the foundation of AWS networking.
- Public and private subnets provide network isolation.
- Route tables determine traffic flow.
- Internet Gateways enable internet connectivity.
- NAT Gateways provide secure outbound access for private resources.
- Security Groups + NACLs together secure the network.
- A well-designed VPC improves security, scalability, and availability.
