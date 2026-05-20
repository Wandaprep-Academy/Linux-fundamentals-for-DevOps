# Module 8: Disk & Storage Management

## Overview
Disk and storage management is a critical skill for DevOps engineers. Whether you are provisioning servers, managing cloud volumes, or troubleshooting storage issues, understanding how Linux handles disks, partitions, filesystems, and mounts is essential.

---

## 8.1 Understanding Disk Storage in Linux

Linux represents every device as a file under `/dev/`. Disks and partitions follow a naming convention:

| Device Name | Description |
|-------------|-------------|
| `/dev/sda` | First SATA/SCSI disk |
| `/dev/sda1` | First partition on first disk |
| `/dev/sdb` | Second disk |
| `/dev/nvme0n1` | First NVMe disk |
| `/dev/nvme0n1p1` | First partition on NVMe disk |
| `/dev/xvda` | Virtual disk (common on AWS EC2) |

---

## 8.2 Inspecting Disks and Partitions

### `lsblk` — List Block Devices
Displays all block devices (disks and partitions) in a tree format.

```bash
lsblk                        # List all block devices
lsblk -f                     # Include filesystem type and UUID
lsblk -o NAME,SIZE,TYPE,MOUNTPOINT   # Custom output columns
```

**Example output:**
```
NAME        MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda           8:0    0   50G  0 disk
├─sda1        8:1    0   49G  0 part /
└─sda2        8:2    0    1G  0 part [SWAP]
sdb           8:16   0  100G  0 disk
```

> Use `lsblk` first to get a quick overview of all attached disks before any disk operation.

---

### `fdisk` — Partition Table Manipulator
Used to view and manage disk partitions (MBR partition tables).

```bash
sudo fdisk -l                # List all disks and partitions
sudo fdisk -l /dev/sda       # List partitions on a specific disk
sudo fdisk /dev/sdb          # Open interactive partition editor for /dev/sdb
```

**Inside the `fdisk` interactive menu:**
| Key | Action |
|-----|--------|
| `p` | Print partition table |
| `n` | Create new partition |
| `d` | Delete a partition |
| `t` | Change partition type |
| `w` | Write changes and exit |
| `q` | Quit without saving |

---

### `gdisk` — GPT Partition Table Editor
Used for disks with GPT partition tables (required for disks > 2TB).

```bash
sudo gdisk -l /dev/sda       # List GPT partitions
sudo gdisk /dev/sdb          # Open interactive GPT partition editor
```

> Use `gdisk` for modern systems and large disks. Use `fdisk` for legacy MBR systems.

---

### `parted` — Advanced Partition Manager
Supports both MBR and GPT, and can resize partitions.

```bash
sudo parted -l               # List all partitions
sudo parted /dev/sdb         # Open interactive mode for /dev/sdb
sudo parted /dev/sdb mklabel gpt          # Create a GPT partition table
sudo parted /dev/sdb mkpart primary ext4 0% 100%   # Create a partition
```

---

## 8.3 Filesystem Management

### Creating a Filesystem (`mkfs`)
After creating a partition, you must format it with a filesystem before use.

```bash
sudo mkfs.ext4 /dev/sdb1         # Format with ext4 (most common for Linux)
sudo mkfs.xfs /dev/sdb1          # Format with XFS (default on RHEL/CentOS)
sudo mkfs.vfat /dev/sdb1         # Format with FAT32 (USB drives)
sudo mkfs -t ext4 /dev/sdb1      # Alternative syntax
```

**Common Filesystem Types:**

| Filesystem | Description | Use Case |
|------------|-------------|----------|
| `ext4` | Default Linux filesystem | General purpose servers |
| `xfs` | High-performance, scalable | RHEL, large files, databases |
| `btrfs` | Modern, supports snapshots | Advanced storage setups |
| `vfat` | FAT32 compatible | USB drives, cross-platform |
| `tmpfs` | RAM-based filesystem | Temporary in-memory storage |

---

### Checking and Repairing a Filesystem (`fsck`)
Checks and repairs filesystem errors. Run only on **unmounted** filesystems.

```bash
sudo fsck /dev/sdb1              # Check and repair filesystem
sudo fsck -y /dev/sdb1           # Auto-answer yes to all prompts
sudo fsck -n /dev/sdb1           # Dry run — check only, no repairs
sudo e2fsck -f /dev/sdb1         # Force check on ext4 filesystem
```

> Never run `fsck` on a mounted filesystem — unmount it first with `umount`.

---

### Viewing Filesystem Information (`tune2fs`, `dumpe2fs`)

```bash
sudo tune2fs -l /dev/sda1        # Show ext4 filesystem details
sudo dumpe2fs /dev/sda1          # Detailed ext2/3/4 filesystem info
```

---

## 8.4 Mounting and Unmounting Filesystems

### `mount` — Attach a Filesystem
Makes a filesystem accessible at a specified directory (mount point).

```bash
sudo mount /dev/sdb1 /mnt/data           # Mount partition to /mnt/data
sudo mount -t ext4 /dev/sdb1 /mnt/data   # Specify filesystem type
sudo mount -o ro /dev/sdb1 /mnt/data     # Mount as read-only
sudo mount -o remount,rw /mnt/data       # Remount as read-write
mount                                    # List all currently mounted filesystems
mount | grep sdb                         # Check if a specific disk is mounted
```

---

### `umount` — Detach a Filesystem

```bash
sudo umount /mnt/data            # Unmount by mount point
sudo umount /dev/sdb1            # Unmount by device name
sudo umount -l /mnt/data         # Lazy unmount (detach when not busy)
sudo umount -f /mnt/data         # Force unmount (use with caution)
```

> Always unmount before removing a disk or USB drive to prevent data corruption.

---

### `/etc/fstab` — Persistent Mounts
Filesystems listed in `/etc/fstab` are automatically mounted at boot.

```bash
cat /etc/fstab                   # View current fstab entries
sudo nano /etc/fstab             # Edit fstab
```

**fstab entry format:**
```
# <device>         <mount point>  <type>  <options>       <dump>  <pass>
/dev/sdb1          /mnt/data      ext4    defaults        0       2
UUID=abc-123       /mnt/backup    xfs     defaults,noatime 0      2
```

**Mount using UUID (recommended):**
```bash
sudo blkid /dev/sdb1             # Get UUID of a partition
```

**After editing fstab, test it:**
```bash
sudo mount -a                    # Mount all entries in fstab (test without rebooting)
```

> Always use UUID instead of device names in fstab — device names can change after reboot.

---

## 8.5 Disk Usage and Space Monitoring

### `df` — Disk Free Space

```bash
df -h                            # Human-readable disk usage for all filesystems
df -hT                           # Include filesystem type
df -h /home                      # Disk usage for a specific mount point
df -i                            # Show inode usage instead of block usage
```

**Example output:**
```
Filesystem      Type  Size  Used Avail Use% Mounted on
/dev/sda1       ext4   50G   20G   28G  42% /
/dev/sdb1       xfs   100G   60G   37G  62% /data
tmpfs           tmpfs  2.0G     0  2.0G   0% /dev/shm
```

> When `Use%` reaches 90%+, investigate immediately to avoid service disruption.

---

### `du` — Disk Usage of Files and Directories

```bash
du -sh /var/log                  # Total size of /var/log
du -sh *                         # Size of all items in current directory
du -ah /home/mike                # All files with human-readable sizes
du -sh /var/* | sort -rh | head -10   # Top 10 largest items in /var
du --max-depth=1 /var            # One level deep summary
```

---

### `ncdu` — Interactive Disk Usage Viewer
A terminal-based interactive disk usage analyzer.

```bash
sudo apt install ncdu            # Install on Ubuntu/Debian
sudo yum install ncdu            # Install on RHEL/CentOS
ncdu /var                        # Analyze /var interactively
ncdu /                           # Analyze entire filesystem
```

> `ncdu` is the fastest way to visually identify what is consuming disk space.

---

## 8.6 Swap Space Management

Swap space is used as overflow when RAM is full.

### Checking Swap

```bash
swapon --show                    # Show active swap spaces
free -h                          # Show RAM and swap usage
cat /proc/swaps                  # Alternative swap info
```

### Creating a Swap File

```bash
# 1. Create a 2GB swap file
sudo fallocate -l 2G /swapfile

# 2. Set correct permissions
sudo chmod 600 /swapfile

# 3. Format as swap
sudo mkswap /swapfile

# 4. Enable the swap file
sudo swapon /swapfile

# 5. Make it persistent (add to fstab)
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
```

### Disabling Swap

```bash
sudo swapoff /swapfile           # Disable a specific swap file
sudo swapoff -a                  # Disable all swap
```

---

## 8.7 Logical Volume Manager (LVM)

LVM allows flexible disk management — resize volumes without downtime.

### LVM Architecture

```
Physical Disks (/dev/sdb, /dev/sdc)
        ↓
Physical Volumes (PV)
        ↓
Volume Group (VG)
        ↓
Logical Volumes (LV)  →  Formatted & Mounted
```

### Physical Volume Commands

```bash
sudo pvcreate /dev/sdb           # Initialize disk as a physical volume
sudo pvdisplay                   # Show all physical volumes
sudo pvs                         # Short summary of physical volumes
sudo pvremove /dev/sdb           # Remove a physical volume
```

### Volume Group Commands

```bash
sudo vgcreate vg_data /dev/sdb           # Create a volume group
sudo vgextend vg_data /dev/sdc           # Add a disk to a volume group
sudo vgdisplay                           # Show all volume groups
sudo vgs                                 # Short summary of volume groups
sudo vgremove vg_data                    # Remove a volume group
```

### Logical Volume Commands

```bash
sudo lvcreate -L 20G -n lv_app vg_data  # Create a 20GB logical volume
sudo lvcreate -l 100%FREE -n lv_data vg_data  # Use all free space
sudo lvdisplay                           # Show all logical volumes
sudo lvs                                 # Short summary of logical volumes
sudo lvremove /dev/vg_data/lv_app        # Remove a logical volume
```

### Extending a Logical Volume (Online Resize)

```bash
# 1. Extend the logical volume by 10GB
sudo lvextend -L +10G /dev/vg_data/lv_app

# 2. Resize the filesystem to use the new space
sudo resize2fs /dev/vg_data/lv_app       # For ext4
sudo xfs_growfs /mnt/app                 # For xfs (use mount point)
```

> LVM is widely used in enterprise Linux and cloud environments for flexible storage management.

---

## 8.8 Working with AWS EBS Volumes (DevOps Context)

When you attach a new EBS volume to an EC2 instance:

```bash
# 1. List block devices to find the new volume
lsblk

# 2. Check if it has a filesystem
sudo file -s /dev/xvdf            # Returns 'data' if unformatted

# 3. Format the volume
sudo mkfs.ext4 /dev/xvdf

# 4. Create a mount point
sudo mkdir /mnt/ebs-data

# 5. Mount the volume
sudo mount /dev/xvdf /mnt/ebs-data

# 6. Get UUID for persistent mount
sudo blkid /dev/xvdf

# 7. Add to /etc/fstab for persistence
echo 'UUID=<your-uuid> /mnt/ebs-data ext4 defaults,nofail 0 2' | sudo tee -a /etc/fstab
```

> Always use the `nofail` option in fstab for cloud volumes — it prevents boot failure if the volume is not attached.

---

## 8.9 Practical Lab Exercises

### Exercise 1: Disk Inspection
```bash
# 1. List all block devices
lsblk -f

# 2. View partition table of the primary disk
sudo fdisk -l /dev/sda

# 3. Check disk usage across all filesystems
df -hT

# 4. Find the top 5 largest directories in /var
du -sh /var/* | sort -rh | head -5
```

### Exercise 2: Create and Mount a New Partition
```bash
# 1. Create a new partition on /dev/sdb (interactive)
sudo fdisk /dev/sdb

# 2. Format the new partition
sudo mkfs.ext4 /dev/sdb1

# 3. Create a mount point and mount it
sudo mkdir /mnt/newdisk
sudo mount /dev/sdb1 /mnt/newdisk

# 4. Verify the mount
df -h /mnt/newdisk
lsblk

# 5. Make it persistent
sudo blkid /dev/sdb1
echo 'UUID=<uuid> /mnt/newdisk ext4 defaults 0 2' | sudo tee -a /etc/fstab
```

### Exercise 3: Create a Swap File
```bash
sudo fallocate -l 1G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
swapon --show
free -h
```

### Exercise 4: LVM Setup
```bash
# Create PV, VG, and LV from /dev/sdb
sudo pvcreate /dev/sdb
sudo vgcreate vg_lab /dev/sdb
sudo lvcreate -L 5G -n lv_test vg_lab
sudo mkfs.ext4 /dev/vg_lab/lv_test
sudo mkdir /mnt/lvm-test
sudo mount /dev/vg_lab/lv_test /mnt/lvm-test
df -h /mnt/lvm-test
```

---

## 8.10 Quick Reference Cheat Sheet

| Command | Description |
|---------|-------------|
| `lsblk -f` | List all block devices with filesystem info |
| `sudo fdisk -l` | List all disk partitions |
| `sudo mkfs.ext4 /dev/sdb1` | Format partition as ext4 |
| `sudo mount /dev/sdb1 /mnt` | Mount a partition |
| `sudo umount /mnt` | Unmount a partition |
| `df -h` | Show disk free space |
| `du -sh /path` | Show directory size |
| `sudo blkid` | Show UUIDs of all partitions |
| `cat /etc/fstab` | View persistent mount config |
| `sudo mount -a` | Mount all fstab entries |
| `swapon --show` | Show active swap |
| `sudo fallocate -l 2G /swapfile` | Create a swap file |
| `sudo pvcreate /dev/sdb` | Create LVM physical volume |
| `sudo vgcreate vg_name /dev/sdb` | Create LVM volume group |
| `sudo lvcreate -L 10G -n lv_name vg_name` | Create logical volume |
| `sudo lvextend -L +5G /dev/vg/lv` | Extend a logical volume |
| `sudo resize2fs /dev/vg/lv` | Resize ext4 after LV extension |
| `ncdu /var` | Interactive disk usage viewer |

---

## Summary

In this module, you learned:
- How Linux represents disks and partitions under `/dev/`
- How to inspect disks with `lsblk`, `fdisk`, `gdisk`, and `parted`
- How to create and manage filesystems with `mkfs` and `fsck`
- How to mount and unmount filesystems and configure persistent mounts via `/etc/fstab`
- How to monitor disk usage with `df`, `du`, and `ncdu`
- How to create and manage swap space
- How to use LVM for flexible, resizable storage
- How to attach and configure AWS EBS volumes on EC2 instances

---

> **Next Module →** Module 9: Linux Networking Fundamentals
