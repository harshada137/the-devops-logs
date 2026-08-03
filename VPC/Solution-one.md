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
