# Task 2 – Storage Expansion Without Downtime (Solution)

# Objective

> Increase the available storage on a running EC2 instance without causing downtime or data loss.

---

# Step 1 – Verify Existing Storage

1. Connect to the EC2 instance using SSH.
2. Check the available block devices.

```bash
lsblk
```

3. Verify the mounted file systems.

```bash
df -h
```

### Why?

> This helps identify the existing disks, mounted file systems, and available storage before attaching a new volume.

---

# Step 2 – Create a New EBS Volume

1. Open the AWS Management Console.
2. Navigate to **EC2 → Volumes**.
3. Click **Create Volume**.
4. Configure the following:

| Setting | Value |
|----------|-------|
| Volume Type | gp3 |
| Size | 20 GiB (or as required) |
| Availability Zone | Same as the EC2 instance |
| Encryption | Enabled |

5. Click **Create Volume**.

### Why?

> The new EBS volume provides additional storage without interrupting the running EC2 instance.

---

# Step 3 – Attach the EBS Volume

1. Select the newly created volume.
2. Click:

```
Actions
→ Attach Volume
```

3. Select the running EC2 instance.
4. Specify the device name (for example):

```
/dev/xvdf
```

5. Click **Attach Volume**.

### Why?

> Attaching the volume makes it available to the operating system while the instance continues running.

---

# Step 4 – Verify the New Volume

Run the following command:

```bash
lsblk
```

Expected output:

```
nvme0n1
└─nvme0n1p1

nvme1n1
```

The new volume should appear as an unformatted disk.

### Why?

> Verifying ensures the operating system has detected the newly attached EBS volume.

---

# Step 5 – Format the Volume

Format the new volume using the ext4 filesystem.

```bash
sudo mkfs -t ext4 /dev/nvme1n1
```

### Why?

> A newly created EBS volume is blank and must be formatted before it can store data.

---

# Step 6 – Create a Mount Point

Create a directory to mount the volume.

```bash
sudo mkdir /data
```

### Why?

> The mount point provides a location in the file system where the new storage will be accessible.

---

# Step 7 – Mount the Volume

Mount the volume.

```bash
sudo mount /dev/nvme1n1 /data
```

Verify the mount.

```bash
df -h
```

### Why?

> Mounting makes the storage available for reading and writing.

---

# Step 8 – Configure Permanent Mount

Retrieve the UUID.

```bash
sudo blkid
```

Open the fstab file.

```bash
sudo nano /etc/fstab
```

Add the following entry.

```text
UUID=<volume-uuid>  /data  ext4  defaults,nofail  0  2
```

Save the file and verify.

```bash
sudo mount -a
```

### Why?

> Configuring `/etc/fstab` ensures the volume is automatically mounted after every reboot.

---

# Step 9 – Move Application Data

Copy the existing application data.

```bash
sudo rsync -avh /var/www/html/ /data/
```

(Optional) Mount the new volume to the original application path.

```bash
sudo mount --bind /data /var/www/html
```

To make the bind mount persistent, add the following entry to `/etc/fstab`.

```text
/data  /var/www/html  none  bind  0  0
```

### Why?

> Moving the application data allows the application to use the new storage without modifying its directory structure.

---

# Step 10 – Verify Persistence

Reboot the EC2 instance.

```bash
sudo reboot
```

Reconnect and verify.

```bash
lsblk
```

```bash
df -h
```

### Why?

> This confirms that the volume is automatically mounted after a system restart.

---

# Step 11 – Create an EBS Snapshot

1. Navigate to:

```
EC2
→ Volumes
```

2. Select the attached volume.
3. Click:

```
Actions
→ Create Snapshot
```

4. Provide a name.
5. Wait until the snapshot status becomes **Completed**.

### Why?

> Snapshots provide a point-in-time backup of the EBS volume for recovery or cloning.

---

# Step 12 – Restore the Snapshot

1. Navigate to:

```
EC2
→ Snapshots
```

2. Select the snapshot.
3. Click:

```
Actions
→ Create Volume
```

4. Select the same Availability Zone.
5. Click **Create Volume**.

### Why?

> Restoring the snapshot creates a new EBS volume containing the backed-up data.

---

# Step 13 – Attach the Restored Volume

Attach the restored volume to the same EC2 instance.

Example device name:

```
/dev/xvdg
```

### Why?

> The restored volume can now be mounted to verify the backup.

---

# Step 14 – Verify the Restored Data

Create a mount point.

```bash
sudo mkdir /restore
```

Mount the restored volume.

```bash
sudo mount /dev/nvme2n1 /restore
```

Verify the data.

```bash
ls -la /restore
```

### Why?

> This confirms that the snapshot restoration was successful and all files were recovered.

---

# Storage Workflow

```text
Running EC2 Instance
        │
        ▼
Create New EBS Volume
        │
        ▼
Attach Volume
        │
        ▼
Format (ext4)
        │
        ▼
Mount at /data
        │
        ▼
Configure /etc/fstab
        │
        ▼
Move Application Data
        │
        ▼
Create Snapshot
        │
        ▼
Restore Snapshot
        │
        ▼
Attach & Verify Restored Volume
```

---

# Verification Commands

## List Block Devices

```bash
lsblk
```

## Check Mounted File Systems

```bash
df -h
```

## View UUID

```bash
blkid
```

## Verify `/etc/fstab`

```bash
cat /etc/fstab
```

---

# Deliverables

- Commands used
- `lsblk` output
- `df -h` output
- `/etc/fstab` configuration
- Snapshot verification
- Restored volume verification

---

# Screenshots to Capture

- Existing storage (`lsblk`)
- New EBS volume
- Attached volume
- Formatted volume
- Mounted volume (`df -h`)
- `/etc/fstab`
- Application data after migration
- Snapshot created
- Restored volume
- Restored data verification

---

# Justification for Every Configuration Choice

| Configuration | Justification |
|--------------|---------------|
| gp3 Volume | Provides high performance at a lower cost than previous generation volumes. |
| Same Availability Zone | EBS volumes can only be attached to EC2 instances in the same AZ. |
| ext4 File System | Stable, reliable, and widely supported Linux file system. |
| `/data` Mount Point | Keeps additional storage separate from the root filesystem. |
| UUID in `/etc/fstab` | Ensures reliable mounting even if device names change. |
| `rsync` | Safely copies application data while preserving permissions and ownership. |
| EBS Snapshot | Provides a point-in-time backup for recovery and disaster recovery. |

---

# Final Outcome

Successfully expanded the storage of a running EC2 instance by attaching a new EBS volume without downtime. The volume was formatted, mounted permanently, application data was migrated successfully, an EBS snapshot was created, restored as a new volume, and the restored data was verified.
