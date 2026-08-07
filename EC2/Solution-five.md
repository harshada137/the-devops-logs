# Task 5 – Create a Reusable EC2 Image

## Objective

Create a reusable Golden AMI from an EC2 instance and verify that a new instance launched from it retains all required configurations.

---

# Prerequisites

- AWS Account
- IAM User with EC2 permissions
- Running EC2 Instance (Amazon Linux 2/Ubuntu)
- SSH Access
- AWS Console

---

# Step 1: Launch an EC2 Instance

1. Log in to the AWS Management Console.
2. Navigate to **EC2 Dashboard**.
3. Click **Launch Instance**.
4. Choose an AMI (Amazon Linux 2 or Ubuntu).
5. Select an instance type (t2.micro if eligible).
6. Configure Security Group:
   - Allow SSH (Port 22)
7. Launch the instance.
8. Connect to the instance using SSH.

---

# Step 2: Install Required Software

Update the system.

### Amazon Linux

```bash
sudo yum update -y
```

### Ubuntu

```bash
sudo apt update && sudo apt upgrade -y
```

Install some sample software.

```bash
sudo yum install -y nginx git
```

or

```bash
sudo apt install -y nginx git
```

Verify installation.

```bash
nginx -v
git --version
```

Create a sample file.

```bash
echo "Golden AMI Test" | sudo tee /home/ec2-user/test.txt
```

For Ubuntu:

```bash
echo "Golden AMI Test" | sudo tee /home/ubuntu/test.txt
```

---

# Step 3: Remove Temporary Files

Delete package cache.

Amazon Linux

```bash
sudo yum clean all
```

Ubuntu

```bash
sudo apt clean
```

Remove temporary files.

```bash
sudo rm -rf /tmp/*
```

(Optional)

```bash
sudo rm -rf /var/tmp/*
```

---

# Step 4: Remove Sensitive Information

Remove SSH history.

```bash
history -c
```

Remove shell history file.

```bash
rm -f ~/.bash_history
```

Remove SSH authorized keys if creating a generic image.

```bash
rm -f ~/.ssh/authorized_keys
```

Clean cloud-init logs (optional).

```bash
sudo cloud-init clean
```

Remove old log files.

```bash
sudo rm -rf /var/log/*.gz
sudo truncate -s 0 /var/log/messages
sudo truncate -s 0 /var/log/secure
```

> **Note:** Do not remove files required by the operating system.

---

# Step 5: Create a Custom AMI

1. Open EC2 Dashboard.
2. Select the instance.
3. Click

```
Actions
→ Image and Templates
→ Create Image
```

4. Enter:

- Image Name
- Description

5. Click **Create Image**.

Wait until the AMI status changes to **Available**.

Record the **AMI ID**.

Example:

```
ami-xxxxxxxxxxxxxxxxx
```

---

# Step 6: Launch a New EC2 Instance Using the Custom AMI

1. Navigate to **AMIs**.
2. Select your custom AMI.
3. Click **Launch Instance from AMI**.
4. Choose instance type.
5. Configure Security Group.
6. Launch the instance.
7. Connect using SSH.

Record the **Instance ID**.

Example:

```
i-xxxxxxxxxxxxxxxxx
```

---

# Step 7: Validate the Configuration

Verify installed software.

```bash
nginx -v
git --version
```

Verify sample file.

Amazon Linux

```bash
cat /home/ec2-user/test.txt
```

Ubuntu

```bash
cat /home/ubuntu/test.txt
```

Verify services.

```bash
systemctl status nginx
```

Confirm that:

- Software is installed
- Configuration files exist
- Test file exists
- System behaves the same as the original instance

---

# Validation Screenshots

Add screenshots of:

- Original EC2 Instance
- Installed software
- AMI Creation
- AMI Available
- Launching new instance
- New EC2 Instance
- SSH into cloned instance
- Installed software verification
- Test file verification

---

# Comparison Between Original and Cloned Instance

| Feature | Original Instance | Cloned Instance |
|----------|-------------------|-----------------|
| OS | Amazon Linux 2 | Amazon Linux 2 |
| Nginx Installed | Yes | Yes |
| Git Installed | Yes | Yes |
| Test File | Present | Present |
| Configuration | Same | Same |
| Packages | Same | Same |

---

# Golden AMI Use Cases

Golden AMIs are useful for:

- Standardized server deployments
- Auto Scaling Groups
- Disaster Recovery
- Development and Testing environments
- Faster server provisioning
- Security compliance
- Production infrastructure consistency

---

# Best Practices for Maintaining Golden AMIs

- Keep the operating system updated.
- Remove temporary files before creating the AMI.
- Remove sensitive information and credentials.
- Apply security patches regularly.
- Use versioning for AMIs.
- Tag AMIs properly.
- Delete outdated AMIs.
- Automate AMI creation using EC2 Image Builder or Packer.
- Test every AMI before production use.

---

# Deliverables

- **AMI ID**

```
ami-xxxxxxxxxxxxxxxx
```

- **New Instance ID**

```
i-xxxxxxxxxxxxxxxx
```

- Validation Screenshots
- Original vs Cloned Instance Comparison
- Golden AMI Use Cases
- Best Practices

---

# Conclusion

> A Golden AMI was successfully created from the configured EC2 instance. A second EC2 instance launched using the custom AMI retained the installed software and configurations, demonstrating that Golden AMIs provide a reliable and efficient way to standardize infrastructure deployment.
