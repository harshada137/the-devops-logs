# Amazon EC2 - Advanced Hands-on Tasks

These tasks are designed for engineers who already have experience working with AWS. The focus is on real-world administration, security, networking, monitoring, and automation rather than basic instance creation.

---

# Task 1 – Build a Production-Ready EC2 Instance

## Objective

Launch an EC2 instance following production best practices.

## Requirements

- Use Amazon Linux 2023
- Choose an appropriate instance type based on workload
- Deploy the instance inside a custom VPC
- Place it in a Public Subnet
- Attach an IAM Role (avoid using access keys)
- Configure Security Groups using the principle of least privilege
- Enable detailed CloudWatch monitoring
- Enable termination protection
- Encrypt the root EBS volume

## Deliverables

- Architecture diagram
- Instance details
- Security Group configuration
- IAM Role attached
- Screenshots
- Justification for every configuration choice

---

# Task 2 – Storage Expansion Without Downtime

## Objective

Increase available storage on a running EC2 instance without data loss.

## Requirements

- Create and attach a new EBS volume
- Format the volume
- Mount it permanently
- Verify persistence after reboot
- Move application data to the new volume
- Create an EBS Snapshot
- Restore the snapshot as another volume and verify its contents

## Deliverables

- Commands used
- lsblk output
- df -h output
- /etc/fstab configuration
- Snapshot verification
- Lessons learned

---

# Task 3 – Secure and Harden an EC2 Instance

## Objective

Apply basic operating system and AWS security hardening.

## Requirements

- Disable password authentication for SSH
- Allow only key-based authentication
- Create a non-root administrative user
- Disable direct root SSH login
- Configure automatic security updates
- Restrict Security Group rules to only required ports
- Install and configure Fail2Ban (or explain why another solution is preferred)
- Verify all changes

## Deliverables

- Configuration files
- Commands used
- Before vs After comparison
- Validation steps

---

# Task 4 – Performance Monitoring and Cost Optimization

## Objective

Analyze an EC2 instance and recommend optimizations.

## Requirements

- Collect CPU utilization
- Collect Memory utilization
- Analyze Disk usage
- Review Network utilization
- Identify underutilized resources
- Recommend a better instance type (if applicable)
- Estimate potential monthly cost savings

## Deliverables

- Monitoring screenshots
- Metrics collected
- Analysis report
- Cost optimization recommendation
- Justification for the proposed instance type

---

# Task 5 – Create a Reusable EC2 Image

## Objective

Prepare an EC2 instance as a reusable golden image.

## Requirements

- Install required software
- Remove temporary files
- Remove sensitive information
- Create a custom AMI
- Launch a second EC2 instance using the custom AMI
- Verify that all configurations are preserved
- Document scenarios where Golden AMIs are useful

## Deliverables

- AMI ID
- New Instance ID
- Validation screenshots
- Comparison between original and cloned instance
- Best practices for maintaining Golden AMIs

---

# Challenge

After completing all five tasks, write a short report answering the following:

- Which task was the most difficult?
- What production challenges could occur while performing it?
- How would you automate this process using Terraform or Ansible?
- Which AWS services could further improve this architecture?
