# Task 3 – Secure and Harden an EC2 Instance (Solution)

# Objective

> Apply operating system and AWS security best practices to harden an Amazon Linux 2023 EC2 instance against unauthorized access and common attacks.

---

# Prerequisites

- Running Amazon Linux 2023 EC2 instance
- SSH access using an existing key pair
- sudo privileges
- Security Group attached to the instance

---

# Step 1 – Check the Current SSH Configuration

View the current SSH configuration.

```bash
sudo grep -E "PasswordAuthentication|PermitRootLogin|PubkeyAuthentication" /etc/ssh/sshd_config
```

### Why?

> Reviewing the existing SSH configuration helps identify insecure settings before applying security hardening.

---

# Step 2 – Disable Password Authentication

Edit the SSH configuration file.

```bash
sudo nano /etc/ssh/sshd_config
```

Update or add the following settings:

```text
PasswordAuthentication no
ChallengeResponseAuthentication no
UsePAM yes
```

Save the file.

### Why?

> Disabling password authentication prevents brute-force password attacks and ensures only users with the private SSH key can access the instance.

---

# Step 3 – Enable Key-Based Authentication

Verify that public key authentication is enabled.

```text
PubkeyAuthentication yes
```

Verify the configuration.

```bash
grep PubkeyAuthentication /etc/ssh/sshd_config
```

### Why?

> Key-based authentication is more secure than password authentication because it relies on cryptographic keys.

---

# Step 4 – Disable Direct Root Login

Edit the SSH configuration.

```bash
sudo nano /etc/ssh/sshd_config
```

Set the following value.

```text
PermitRootLogin no
```

### Why?

> Preventing direct root login reduces the risk of unauthorized administrative access.

---

# Step 5 – Create a Dedicated Administrative User

Create a new user.

```bash
sudo adduser adminuser
```

(Optional) Set a password.

```bash
sudo passwd adminuser
```

Grant sudo privileges.

```bash
sudo usermod -aG wheel adminuser
```

Verify.

```bash
groups adminuser
```

Expected Output:

```text
adminuser : adminuser wheel
```

### Why?

> Using a dedicated administrative user follows the Principle of Least Privilege and improves accountability.

---

# Step 6 – Copy the SSH Key

Create the SSH directory.

```bash
sudo mkdir -p /home/adminuser/.ssh
```

Copy the authorized key.

```bash
sudo cp ~/.ssh/authorized_keys /home/adminuser/.ssh/
```

Update ownership.

```bash
sudo chown -R adminuser:adminuser /home/adminuser/.ssh
```

Set the required permissions.

```bash
sudo chmod 700 /home/adminuser/.ssh
sudo chmod 600 /home/adminuser/.ssh/authorized_keys
```

### Why?

> The new user must have the same authorized public key to securely access the instance using SSH.

---

# Step 7 – Test the New User

Open a new terminal and connect using the new user.

```bash
ssh -i my-key.pem adminuser@<Public-IP>
```

Verify the login before closing the original SSH session.

### Why?

> Testing ensures the new user can successfully authenticate before disabling the existing administrative access.

---

# Step 8 – Restart the SSH Service

Restart the SSH daemon.

```bash
sudo systemctl restart sshd
```

Verify the service.

```bash
sudo systemctl status sshd
```

Expected Output:

```text
Active: active (running)
```

### Why?

> Restarting the SSH service applies the updated security configuration.

---

# Step 9 – Configure Automatic Security Updates

Install the automatic update package.

```bash
sudo dnf install -y dnf-automatic
```

Enable automatic updates.

```bash
sudo systemctl enable --now dnf-automatic.timer
```

Verify the service.

```bash
systemctl status dnf-automatic.timer
```

Expected Output:

```text
Active: active (waiting)
```

### Why?

> Automatic updates ensure that important security patches are installed regularly.

---

# Step 10 – Restrict Security Group Rules

Navigate to:

```text
AWS Console
→ EC2
→ Security Groups
```

Configure the inbound rules.

| Port | Protocol | Source | Purpose |
|------|----------|---------|----------|
| 22 | TCP | Your Public IP (/32) | SSH |
| 80 | TCP | 0.0.0.0/0 | HTTP |
| 443 | TCP | 0.0.0.0/0 | HTTPS |

Remove any unnecessary inbound rules such as:

- FTP (21)
- Telnet (23)
- RDP (3389) if not required
- MySQL (3306) exposed publicly
- PostgreSQL (5432) exposed publicly

### Why?

> Restricting inbound access reduces the attack surface and follows AWS security best practices.

---

# Step 11 – Install Fail2Ban

Install EPEL.

```bash
sudo dnf install -y epel-release
```

Install Fail2Ban.

```bash
sudo dnf install -y fail2ban
```

Enable the service.

```bash
sudo systemctl enable --now fail2ban
```

Create a local configuration.

```bash
sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
```

Edit the configuration.

```bash
sudo nano /etc/fail2ban/jail.local
```

Configure the SSH jail.

```ini
[sshd]
enabled = true
port = ssh
maxretry = 5
findtime = 10m
bantime = 1h
```

Restart the service.

```bash
sudo systemctl restart fail2ban
```

Verify.

```bash
sudo fail2ban-client status sshd
```

### Why?

> Fail2Ban protects the server by temporarily blocking IP addresses after repeated failed login attempts.

---

# Architecture Diagram

```text
                     Internet
                         │
                 Security Group
          (22 from My IP, 80, 443)
                         │
        ┌─────────────────────────────────┐
        │      Amazon Linux 2023 EC2      │
        │                                 │
        │ SSH Key Authentication Enabled  │
        │ Password Login Disabled         │
        │ Root Login Disabled             │
        │ Dedicated Admin User            │
        │ Automatic Security Updates      │
        │ Fail2Ban Enabled                │
        └─────────────────────────────────┘
```

---

# Configuration Files

## `/etc/ssh/sshd_config`

```text
PasswordAuthentication no
PubkeyAuthentication yes
PermitRootLogin no
ChallengeResponseAuthentication no
UsePAM yes
```

---

## `/etc/fail2ban/jail.local`

```ini
[sshd]
enabled = true
port = ssh
maxretry = 5
findtime = 10m
bantime = 1h
```

---

# Verification

## Verify Password Authentication

```bash
grep PasswordAuthentication /etc/ssh/sshd_config
```

Expected Output:

```text
PasswordAuthentication no
```

---

## Verify Root Login

```bash
grep PermitRootLogin /etc/ssh/sshd_config
```

Expected Output:

```text
PermitRootLogin no
```

---

## Verify New Administrative User

```bash
id adminuser
```

---

## Verify SSH Service

```bash
systemctl status sshd
```

Expected:

```text
Active: active (running)
```

---

## Verify Automatic Updates

```bash
systemctl status dnf-automatic.timer
```

Expected:

```text
Active: active (waiting)
```

---

## Verify Fail2Ban

```bash
sudo fail2ban-client status sshd
```

Expected:

```text
Status for the jail: sshd
Currently banned: 0
```

---

## Verify Security Group

Navigate to:

```text
EC2
→ Security Groups
→ Inbound Rules
```

Ensure only the required ports are open.

---

# Commands Used

```bash
grep
nano
adduser
passwd
usermod
mkdir
cp
chown
chmod
systemctl
dnf install
fail2ban-client
id
groups
```

---

# Before vs After Comparison

| Feature | Before | After |
|----------|---------|--------|
| Password Authentication | Enabled | Disabled |
| SSH Authentication | Password + Key | Key Only |
| Root Login | Allowed | Disabled |
| Administrative User | Root | Dedicated sudo user |
| Automatic Updates | Disabled | Enabled |
| Security Group | Multiple/Open Ports | Required Ports Only |
| Brute Force Protection | None | Fail2Ban Enabled |

---

# Screenshots to Capture

- SSH configuration (`sshd_config`)
- New administrative user
- Successful SSH login using the new user
- SSH service status
- Automatic updates service
- Fail2Ban status
- Security Group inbound rules

---

# Justification for Every Configuration Choice

| Configuration | Justification |
|--------------|---------------|
| Key-Based Authentication | Eliminates password-based attacks and improves security. |
| Disable Password Authentication | Prevents brute-force password attacks. |
| Disable Root Login | Prevents direct administrative access. |
| Dedicated Admin User | Follows the Principle of Least Privilege. |
| Automatic Updates | Ensures security patches are installed regularly. |
| Restricted Security Group | Reduces the attack surface by allowing only required traffic. |
| Fail2Ban | Protects against repeated SSH login attempts and brute-force attacks. |

---

# AWS Security Best Practice

In production environments, AWS recommends combining operating system hardening with AWS security services such as:

- Security Groups
- IAM Roles
- AWS Systems Manager Session Manager
- AWS WAF
- AWS Shield
- Amazon GuardDuty

Fail2Ban provides an additional layer of host-level protection and is commonly used for Linux server hardening.

---

# Final Outcome

Successfully hardened an Amazon Linux 2023 EC2 instance by:

- Enabling key-based SSH authentication
- Disabling password authentication
- Disabling direct root login
- Creating a dedicated administrative user
- Configuring automatic security updates
- Restricting Security Group access
- Enabling Fail2Ban for SSH protection
- Verifying all security configurations

The EC2 instance is now more secure and follows AWS and Linux security best practices against unauthorized access and common attacks.
