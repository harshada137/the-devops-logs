# Task 3 – Secure and Harden an EC2 Instance

## Objective

Apply operating system and AWS security best practices to harden an EC2 instance against unauthorized access and common attacks.

---

# Prerequisites

- Running Amazon Linux 2023 EC2 instance
- SSH access using an existing key pair
- sudo privileges
- Security Group attached to the instance

---

# Step 1 – Check Current SSH Configuration

View the SSH daemon configuration.

```bash
sudo grep -E "PasswordAuthentication|PermitRootLogin" /etc/ssh/sshd_config
```

Example Output:

```
#PasswordAuthentication yes
#PermitRootLogin yes
```

---

# Step 2 – Disable Password Authentication

Edit the SSH configuration.

```bash
sudo nano /etc/ssh/sshd_config
```

Find or add the following:

```text
PasswordAuthentication no
ChallengeResponseAuthentication no
UsePAM yes
```

Save the file.

---

# Step 3 – Allow Only Key-Based Authentication

Ensure the following setting exists:

```text
PubkeyAuthentication yes
```

Verify:

```bash
grep PubkeyAuthentication /etc/ssh/sshd_config
```

---

# Step 4 – Disable Direct Root SSH Login

Edit the SSH configuration.

```bash
sudo nano /etc/ssh/sshd_config
```

Set:

```text
PermitRootLogin no
```

---

# Step 5 – Create a Non-Root Administrative User

Create a new user.

```bash
sudo adduser adminuser
```

Set a password (optional for console login).

```bash
sudo passwd adminuser
```

Grant sudo privileges.

```bash
sudo usermod -aG wheel adminuser
```

Verify:

```bash
groups adminuser
```

Expected Output:

```
adminuser : adminuser wheel
```

---

# Step 6 – Copy SSH Key to the New User

Create the SSH directory.

```bash
sudo mkdir -p /home/adminuser/.ssh
```

Copy the authorized key.

```bash
sudo cp ~/.ssh/authorized_keys /home/adminuser/.ssh/
```

Set ownership.

```bash
sudo chown -R adminuser:adminuser /home/adminuser/.ssh
```

Set permissions.

```bash
sudo chmod 700 /home/adminuser/.ssh
sudo chmod 600 /home/adminuser/.ssh/authorized_keys
```

---

# Step 7 – Test the New User

Open a new terminal.

```bash
ssh -i my-key.pem adminuser@<Public-IP>
```

Ensure login is successful before closing the original session.

---

# Step 8 – Restart SSH Service

Restart SSH to apply changes.

```bash
sudo systemctl restart sshd
```

Check status.

```bash
sudo systemctl status sshd
```

Expected Output:

```
Active: active (running)
```

---

# Step 9 – Configure Automatic Security Updates

Amazon Linux 2023 uses **dnf**.

Install automatic update package.

```bash
sudo dnf install -y dnf-automatic
```

Enable automatic updates.

```bash
sudo systemctl enable --now dnf-automatic.timer
```

Verify.

```bash
systemctl status dnf-automatic.timer
```

Expected Output:

```
Active: active (waiting)
```

---

# Step 10 – Restrict Security Group Rules

Navigate to:

```
AWS Console
→ EC2
→ Security Groups
```

Recommended Inbound Rules

| Port | Protocol | Source | Purpose |
|------|----------|---------|----------|
|22|TCP|Your Public IP (/32)|SSH|
|80|TCP|0.0.0.0/0|HTTP|
|443|TCP|0.0.0.0/0|HTTPS|

Remove unnecessary ports such as:

- 21 (FTP)
- 23 (Telnet)
- 3389 (RDP) if not required
- 3306 (MySQL) exposed to the Internet
- 5432 (PostgreSQL) exposed publicly

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

Create local configuration.

```bash
sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
```

Edit.

```bash
sudo nano /etc/fail2ban/jail.local
```

Configure SSH protection.

```ini
[sshd]
enabled = true
port = ssh
maxretry = 5
findtime = 10m
bantime = 1h
```

Restart.

```bash
sudo systemctl restart fail2ban
```

Verify.

```bash
sudo fail2ban-client status sshd
```

---

## Note on AWS Best Practice

While Fail2Ban provides host-level brute-force protection, AWS security best practices typically rely on:

- Security Groups
- AWS WAF
- AWS Shield
- IAM
- AWS Systems Manager Session Manager (to eliminate SSH access)

Fail2Ban is still acceptable for Linux server hardening and learning purposes.

---

# Step 12 – Verify Security Configuration

Verify password authentication.

```bash
grep PasswordAuthentication /etc/ssh/sshd_config
```

Expected:

```
PasswordAuthentication no
```

---

Verify root login.

```bash
grep PermitRootLogin /etc/ssh/sshd_config
```

Expected:

```
PermitRootLogin no
```

---

Verify new admin user.

```bash
id adminuser
```

---

Verify automatic updates.

```bash
systemctl status dnf-automatic.timer
```

---

Verify Fail2Ban.

```bash
sudo fail2ban-client status
```

---

Verify Security Group.

AWS Console

```
EC2
→ Security Groups
→ Inbound Rules
```

Ensure only required ports are open.

---

# Deliverables

## Configuration Files

### `/etc/ssh/sshd_config`

```text
PasswordAuthentication no
PubkeyAuthentication yes
PermitRootLogin no
ChallengeResponseAuthentication no
UsePAM yes
```

---

### `/etc/fail2ban/jail.local`

```ini
[sshd]
enabled = true
port = ssh
maxretry = 5
findtime = 10m
bantime = 1h
```

---

## Commands Used

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
|Password SSH Login|Enabled|Disabled|
|SSH Authentication|Password + Key|Key Only|
|Root SSH Login|Allowed|Disabled|
|Administrative User|Root|Dedicated sudo user|
|Automatic Updates|Disabled|Enabled|
|Security Group|Multiple/Open Ports|Required Ports Only|
|Brute Force Protection|None|Fail2Ban Enabled|

---

# Validation Steps

## Test SSH Login

Attempt login without the private key.

```bash
ssh ec2-user@<Public-IP>
```

Expected:

```
Permission denied (publickey)
```

---

## Test New Admin User

```bash
ssh -i my-key.pem adminuser@<Public-IP>
```

Expected:

```
Login Successful
```

---

## Verify Root Login

```bash
ssh root@<Public-IP>
```

Expected:

```
Permission denied
```

---

## Verify Automatic Updates

```bash
systemctl status dnf-automatic.timer
```

Expected:

```
Active: active (waiting)
```

---

## Verify Fail2Ban

```bash
sudo fail2ban-client status sshd
```

Expected:

```
Status for the jail: sshd
Currently banned: 0
```

---

## Verify Security Group

Confirm only these ports are open:

- 22 (SSH) from your IP only
- 80 (HTTP)
- 443 (HTTPS)

No unnecessary inbound ports should be exposed.

---

# Lessons Learned

- Key-based SSH authentication is significantly more secure than password-based authentication.
- Disabling direct root login reduces the risk of unauthorized administrative access.
- Creating a dedicated sudo user improves accountability and follows the principle of least privilege.
- Automatic security updates help protect the system against newly discovered vulnerabilities.
- Restricting Security Group rules minimizes the attack surface.
- Fail2Ban helps mitigate brute-force attacks by temporarily banning repeated failed login attempts.
- Combining OS-level hardening with AWS Security Groups provides a layered security approach.
