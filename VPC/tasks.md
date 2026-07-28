# Amazon VPC - Hands-on Tasks

These tasks are designed to simulate real-world networking scenarios commonly encountered by DevOps Engineers and Cloud Engineers.

> **Note:** Complete each task independently and document your solution, architecture diagram, screenshots, commands (if applicable), observations, challenges faced, and key learnings.

---

# Task 1 - Design and Build a Production-Ready VPC

## Objective

Create a highly available VPC suitable for hosting a production web application.

## Requirements

- Create a Custom VPC with CIDR `10.0.0.0/16`
- Create:
  - 2 Public Subnets (different AZs)
  - 2 Private Application Subnets (different AZs)
  - 2 Private Database Subnets (different AZs)
- Attach an Internet Gateway
- Create Route Tables
- Associate appropriate Route Tables with each subnet
- Ensure only public subnets have direct internet access

## Deliverables

- Architecture Diagram
- VPC Configuration
- CIDR Planning
- Subnet Details
- Route Tables
- Screenshots
- Explain why this architecture is considered highly available.

---

# Task 2 - Configure Secure Internet Access for Private Instances

## Objective

Allow EC2 instances in private subnets to access the internet without exposing them publicly.

## Requirements

- Deploy an EC2 instance in a Public Subnet
- Deploy another EC2 instance in a Private Subnet
- Configure a NAT Gateway
- Allocate an Elastic IP
- Update Route Tables
- Verify:
  - Private EC2 can download software
  - Private EC2 cannot be accessed directly from the internet

## Deliverables

- Architecture Diagram
- Route Table Configuration
- Verification Commands
- Screenshots
- Explain why NAT Gateway is preferred over placing every EC2 in a Public Subnet.

---

# Task 3 - Build and Secure a Three-Tier Architecture

## Objective

Implement a secure three-tier application architecture using Security Groups.

## Requirements

Deploy:

- Web Server (Public Subnet)
- Application Server (Private Subnet)
- Database Server (Private Database Subnet)

Configure Security Groups so that:

- Internet → Web Server (HTTP/HTTPS)
- Web Server → Application Server
- Application Server → Database
- No direct internet access to Application or Database servers

Validate connectivity between each layer.

## Deliverables

- Architecture Diagram
- Security Group Rules
- Connectivity Test Results
- Explain how Security Groups enforce least-privilege access.

---

# Task 4 - Troubleshoot Network Connectivity Issues

## Objective

Identify and resolve common VPC networking problems.

## Scenarios

Troubleshoot the following issues one by one:

- EC2 cannot access the internet
- SSH connection fails
- HTTP service is unreachable
- Route Table misconfiguration
- Incorrect Security Group rules
- NACL blocking traffic
- Missing Internet Gateway
- NAT Gateway route missing

## Deliverables

For each issue provide:

- Root Cause
- Troubleshooting Steps
- Resolution
- Verification
- Lessons Learned

---

# Task 5 - Monitor and Analyze Network Traffic

## Objective

Use VPC Flow Logs to analyze network traffic and investigate connectivity.

## Requirements

- Enable VPC Flow Logs
- Generate:
  - Successful traffic
  - Blocked traffic
- Capture logs in CloudWatch Logs
- Identify:
  - Source IP
  - Destination IP
  - Protocol
  - Port
  - ACCEPT / REJECT actions
- Investigate one failed connection using Flow Logs

## Deliverables

- Flow Log Configuration
- Sample Log Entries
- Analysis of Traffic
- Screenshots
- Explain how VPC Flow Logs help during production incidents.

---

# Bonus Challenge

You have been asked to design networking for a production application with the following requirements:

- Highly Available
- Multi-AZ
- Public-facing Web Application
- Private Application Layer
- Private Database Layer
- Secure Internet Access for Private Resources
- Future EKS Deployment
- Minimal Public Exposure

## Your Task

Design the complete AWS networking architecture.

Include:

- CIDR Planning
- Subnet Design
- Route Tables
- Security Groups
- Internet Gateway
- NAT Gateway
- Future Scalability Considerations
- Best Practices

Draw the architecture diagram and justify every design decision as if you were presenting it during an architecture review.

---

## Expected Outcome

After completing these tasks, you should be able to:

- Design production-ready VPC architectures
- Configure secure networking
- Troubleshoot connectivity issues
- Implement least-privilege network access
- Analyze network traffic using VPC Flow Logs
- Explain AWS networking concepts confidently in interviews
