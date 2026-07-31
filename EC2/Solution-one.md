# Task 1 – Build a Production-Ready EC2 Instance (Solution)

# Objective

> Launch a production-ready EC2 instance by following AWS best practices for security, monitoring, availability, and management.

---

# Step 1 – Create a Custom VPC

1. Open the AWS Management Console.
2. Navigate to **VPC**.
3. Click **Create VPC**.
4. Configure the following:

| Setting | Value |
|----------|-------|
| Name | prod-vpc |
| IPv4 CIDR | 10.0.0.0/16 |
| IPv6 | None |
| Tenancy | Default |

5. Click **Create VPC**.

### Why?

> A custom VPC provides complete control over networking instead of using the default VPC. It allows better security, subnet planning, and scalability.

---

# Step 2 – Create a Public Subnet

1. Go to **Subnets**.
2. Click **Create Subnet**.
3. Select **prod-vpc**.
4. Configure:

| Setting | Value |
|----------|-------|
| Name | public-subnet-1 |
| Availability Zone | ap-south-1a |
| CIDR | 10.0.1.0/24 |

5. Enable **Auto Assign Public IPv4 Address**.

### Why?

> The EC2 instance needs internet connectivity, so it is placed inside a public subnet.

---

# Step 3 – Create and Attach an Internet Gateway

1. Navigate to **Internet Gateways**.
2. Click **Create Internet Gateway**.
3. Name it:

```
prod-igw
```

4. Attach it to **prod-vpc**.

### Why?

> The Internet Gateway allows resources inside the public subnet to communicate with the internet.

---

# Step 4 – Configure Route Table

1. Create a Route Table.

| Setting | Value |
|----------|-------|
| Name | public-route-table |

2. Associate it with **public-subnet-1**.

3. Add the following route:

| Destination | Target |
|-------------|--------|
| 0.0.0.0/0 | Internet Gateway |

### Why?

> Without this route, the instance cannot access the internet.

---

# Step 5 – Create an IAM Role

1. Open **IAM**.
2. Click **Roles**.
3. Create a role for:

```
AWS Service → EC2
```

4. Attach policies:

- AmazonSSMManagedInstanceCore
- CloudWatchAgentServerPolicy (Optional if CloudWatch Agent is used)

5. Name the role:

```
EC2-Production-Role
```

### Why?

> IAM Roles eliminate the need for storing AWS Access Keys on the instance, improving security.

---

# Step 6 – Create a Security Group

Create a Security Group with the following rules.

### Inbound Rules

| Type | Port | Source | Reason |
|------|------|--------|--------|
| SSH | 22 | Your Public IP only | Secure administration |
| HTTP | 80 | 0.0.0.0/0 | Allow web traffic |
| HTTPS | 443 | 0.0.0.0/0 | Secure web traffic |

### Outbound Rules

| Type | Destination |
|------|-------------|
| All Traffic | 0.0.0.0/0 |

### Why?

> Only the minimum required ports are opened following the Principle of Least Privilege.

---

# Step 7 – Launch the EC2 Instance

Navigate to **EC2 → Launch Instance**.

Configure the instance as follows.

| Setting | Value |
|----------|-------|
| Name | prod-web-server |
| AMI | Amazon Linux 2023 |
| Instance Type | t3.medium |
| Key Pair | Existing or New |
| Network | prod-vpc |
| Subnet | public-subnet-1 |
| Auto Assign Public IP | Enabled |
| Security Group | prod-web-sg |
| IAM Role | EC2-Production-Role |

### Why Amazon Linux 2023?

> - Latest AWS-supported operating system
> - Improved security
> - Long-term support
> - Better package management
> - Optimized for AWS services

### Why t3.medium?

> - 2 vCPUs
> - 4 GB RAM
> - Suitable for small production web applications
> - Cost-effective
> - Burstable performance

---

# Step 8 – Encrypt the Root EBS Volume

During launch:

1. Expand **Storage**.
2. Select the Root Volume.
3. Enable:

```
Encryption = Enabled
```

4. Use the default AWS-managed KMS key.

### Why?

> Encryption protects data at rest and satisfies security and compliance requirements.

---

# Step 9 – Enable Detailed CloudWatch Monitoring

Under **Advanced Details**:

Enable

```
Detailed CloudWatch Monitoring
```

### Why?

> Detailed monitoring provides metrics every 1 minute instead of every 5 minutes, enabling faster issue detection.

---

# Step 10 – Enable Termination Protection

After the instance launches:

1. Select the instance.
2. Click

```
Actions
→ Instance Settings
→ Change Termination Protection
```

3. Enable it.

### Why?

> Prevents accidental deletion of production servers.

---

# Step 11 – Connect to the Instance

Using SSH:

```bash
ssh -i my-key.pem ec2-user@<Public-IP>
```

Verify the OS:

```bash
cat /etc/os-release
```

Expected Output:

```
Amazon Linux 2023
```

---

# Step 12 – Verify IAM Role

Run:

```bash
curl http://169.254.169.254/latest/meta-data/iam/security-credentials/
```

Expected Output:

```
EC2-Production-Role
```

This confirms that the IAM Role is attached successfully.

---

# Step 13 – Verify Monitoring

Go to:

```
CloudWatch
→ Metrics
→ EC2
```

Verify that 1-minute metrics are being generated.

---

# Step 14 – Verify Encryption

Navigate to:

```
EC2
→ Volumes
```

Check the Root Volume.

Expected:

```
Encrypted = Yes
```

---

# Step 15 – Verify Termination Protection

Go to:

```
EC2
→ Actions
→ Instance Settings
```

Termination Protection should show:

```
Enabled
```

---

# Architecture Diagram

```text
                     Internet
                         │
                  Internet Gateway
                         │
                Public Route Table
                         │
        ┌────────────────────────────────┐
        │          Custom VPC            │
        │        10.0.0.0/16             │
        │                                │
        │   Public Subnet                │
        │   10.0.1.0/24                  │
        │                                │
        │   ┌───────────────────────┐    │
        │   │ Amazon Linux 2023 EC2 │    │
        │   │                       │    │
        │   │ IAM Role Attached     │    │
        │   │ SG Applied            │    │
        │   │ Encrypted EBS         │    │
        │   │ Detailed Monitoring   │    │
        │   │ Termination Protected │    │
        │   └───────────────────────┘    │
        └────────────────────────────────┘
```

---

# Instance Details

| Property | Value |
|----------|-------|
| Instance Name | prod-web-server |
| Operating System | Amazon Linux 2023 |
| Instance Type | t3.medium |
| Network | Custom VPC |
| Subnet | Public Subnet |
| Public IP | Enabled |
| Root Volume | Encrypted |
| IAM Role | EC2-Production-Role |
| Monitoring | Detailed |
| Termination Protection | Enabled |

---

# Security Group Configuration

## Inbound Rules

| Protocol | Port | Source |
|----------|------|--------|
| SSH | 22 | Your Public IP |
| HTTP | 80 | Anywhere |
| HTTPS | 443 | Anywhere |

## Outbound Rules

| Protocol | Destination |
|----------|-------------|
| All Traffic | Anywhere |

---

# IAM Role Attached

```
EC2-Production-Role
```

Attached Policies:

- AmazonSSMManagedInstanceCore
- CloudWatchAgentServerPolicy

---

# Screenshots to Capture

- Custom VPC
- Public Subnet
- Internet Gateway
- Route Table
- Security Group
- IAM Role
- EC2 Launch Configuration
- EC2 Instance Running
- Storage showing Encryption Enabled
- CloudWatch Detailed Monitoring
- Termination Protection Enabled
- SSH Login
- IAM Role Verification
- CloudWatch Metrics

---

# Justification for Every Configuration Choice

| Configuration | Justification |
|--------------|---------------|
| Amazon Linux 2023 | Latest AWS-supported operating system with improved security and performance. |
| t3.medium | Suitable for small production workloads while remaining cost-effective. |
| Custom VPC | Provides complete control over networking and security. |
| Public Subnet | Required because the instance needs internet access. |
| Internet Gateway | Enables internet connectivity for the public subnet. |
| IAM Role | Eliminates the need to store AWS Access Keys on the server. |
| Security Group | Restricts access to only necessary ports following the Principle of Least Privilege. |
| Encrypted EBS | Protects data at rest and meets compliance requirements. |
| Detailed Monitoring | Provides 1-minute metrics for quicker monitoring and alerting. |
| Termination Protection | Prevents accidental deletion of production resources. |
| AWS Managed KMS Key | Simplifies encryption management while maintaining security. |

---

# Final Outcome

Successfully deployed a production-ready EC2 instance with:

- Amazon Linux 2023
- Custom VPC
- Public Subnet
- Internet Gateway
- Route Table
- IAM Role
- Least Privilege Security Group
- Encrypted Root EBS Volume
- Detailed CloudWatch Monitoring
- Termination Protection Enabled
- Internet Connectivity
- Secure SSH Access
- Production Best Practices
