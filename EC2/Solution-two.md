# Task 2 – Storage Expansion Without Downtime

## Objective

> Increase the available storage on a running EC2 instance without causing downtime or data loss.

---

# Prerequisites

- Running EC2 instance (Amazon Linux 2023)
- SSH access to the instance
- IAM permissions to create and attach EBS volumes and snapshots

---

# Step 1 – Verify Existing Storage

Check the currently attached disks.

```bash
lsblk
```

Check mounted file systems.

```bash
df -h
```

Example:

```
NAME         MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
nvme0n1      259:0    0   8G  0 disk
├─nvme0n1p1  259:1    0   8G  0 part /
```

---

# Step 2 – Create a New EBS Volume

1. Open AWS Console.
2. Go to **EC2 → Volumes**.
3. Click **Create Volume**.

Configuration:

- Volume Type: gp3
- Size: 20 GiB (or as required)
- Availability Zone: Same as EC2 instance
- Encryption: Enabled

Click **Create Volume**.

---

# Step 3 – Attach the Volume

1. Select the new volume.
2. Click **Actions → Attach Volume**.
3. Select the running EC2 instance.
4. Device Name:

```
/dev/xvdf
```

Attach the volume.

---

# Step 4 – Verify the New Disk

SSH into the EC2 instance.

Run:

```bash
lsblk
```

Example:

```
NAME         MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
nvme0n1      259:0    0   8G  0 disk
├─nvme0n1p1  259:1    0   8G  0 part /
nvme1n1      259:2    0  20G  0 disk
```

The new disk is **not yet formatted**.

---

# Step 5 – Format the Volume

Create an ext4 filesystem.

```bash
sudo mkfs -t ext4 /dev/nvme1n1
```

Expected output:

```
Creating filesystem...
Writing superblocks...
done
```

---

# Step 6 – Create Mount Directory

```bash
sudo mkdir /data
```

---

# Step 7 – Mount the Volume

```bash
sudo mount /dev/nvme1n1 /data
```

Verify:

```bash
df -h
```

Expected:

```
Filesystem      Size Used Avail Mounted on
/dev/nvme1n1     20G   24M   19G /data
```

---

# Step 8 – Make Mount Persistent

Get the UUID.

```bash
sudo blkid
```

Example:

```
UUID="8d97f5f7-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

Edit fstab.

```bash
sudo nano /etc/fstab
```

Add:

```
UUID=8d97f5f7-xxxx-xxxx-xxxx-xxxxxxxxxxxx  /data  ext4  defaults,nofail  0  2
```

Save and exit.

Verify:

```bash
sudo mount -a
```

No output means success.

---

# Step 9 – Move Application Data

Assume application data exists in:

```
/var/www/html
```

Copy data while preserving permissions.

```bash
sudo rsync -avh /var/www/html/ /data/
```

Rename original folder.

```bash
sudo mv /var/www/html /var/www/html_old
```

Create a new directory.

```bash
sudo mkdir /var/www/html
```

Mount the new volume.

```bash
sudo mount --bind /data /var/www/html
```

(Optional) Add bind mount in `/etc/fstab`.

```
/data   /var/www/html   none   bind   0 0
```

Verify:

```bash
ls /var/www/html
```

Application files should be present.

---

# Step 10 – Verify Persistence

Reboot the instance.

```bash
sudo reboot
```

Reconnect.

Check mounts.

```bash
df -h
```

Check disks.

```bash
lsblk
```

Ensure:

- Volume is mounted automatically.
- Application data is available.

---

# Step 11 – Create an EBS Snapshot

AWS Console:

1. EC2
2. Volumes
3. Select Volume
4. Actions
5. Create Snapshot

Name:

```
app-data-backup
```

Wait until snapshot status becomes:

```
Completed
```

---

# Step 12 – Restore Snapshot

1. Open Snapshots.
2. Select Snapshot.
3. Actions → Create Volume.

Configuration:

- Same Availability Zone
- gp3

Create the volume.

---

# Step 13 – Attach Restored Volume

Attach the restored volume to the EC2 instance.

Example device:

```
/dev/xvdg
```

---

# Step 14 – Verify Restored Data

Check disks.

```bash
lsblk
```

Create mount point.

```bash
sudo mkdir /restore
```

Mount restored volume.

```bash
sudo mount /dev/nvme2n1 /restore
```

Verify files.

```bash
ls -la /restore
```

All application files should be present.

---

# Verification Commands

## List Block Devices

```bash
lsblk
```

---

## Check Mounted Filesystems

```bash
df -h
```

---

## Check UUID

```bash
blkid
```

---

## Verify fstab

```bash
cat /etc/fstab
```

---

## Verify Snapshot

AWS Console:

```
EC2
 └── Snapshots
      └── Status = Completed
```

---

# Deliverables

## Commands Used

```bash
lsblk
df -h
mkfs.ext4
mkdir
mount
blkid
nano /etc/fstab
mount -a
rsync
mv
reboot
ls
```

---

## Sample lsblk Output

```
NAME         MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
nvme0n1      259:0    0   8G  0 disk
├─nvme0n1p1  259:1    0   8G  0 part /
nvme1n1      259:2    0  20G  0 disk /data
nvme2n1      259:3    0  20G  0 disk /restore
```

---

## Sample df -h Output

```
Filesystem      Size Used Avail Use% Mounted on
/dev/nvme0n1p1   8G  2.3G  5.7G  29% /
/dev/nvme1n1    20G  1.5G   19G   8% /data
/dev/nvme2n1    20G  1.5G   19G   8% /restore
```

---

## Sample `/etc/fstab`

```
UUID=8d97f5f7-xxxx-xxxx-xxxx-xxxxxxxxxxxx /data ext4 defaults,nofail 0 2
/data /var/www/html none bind 0 0
```

---

## Snapshot Verification

- Snapshot created successfully.
- Snapshot state is **Completed**.
- Restored a new EBS volume from the snapshot.
- Attached the restored volume to the EC2 instance.
- Mounted the restored volume.
- Verified all application files were present and intact.

---

# Lessons Learned

- EBS volumes can be attached to running EC2 instances without downtime.
- New EBS volumes must be formatted before use.
- UUIDs should be used in `/etc/fstab` to ensure reliable mounting across reboots.
- `mount -a` is a safe way to validate `/etc/fstab` before rebooting.
- `rsync` is preferred over `cp` because it preserves permissions, ownership, timestamps, and supports resumable transfers.
- EBS snapshots provide point-in-time backups and can be restored into new volumes.
- Snapshot restores are useful for disaster recovery, testing, and cloning application data.
- Always verify data integrity after mounting restored volumes.
