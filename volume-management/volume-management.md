# Linux Volume Management

---

## Overview

In Linux (running on EC2), storage is organized in **blocks** — these are called **volumes**.

In AWS, the service that provides additional block storage is **EBS (Elastic Block Storage)**, which allows you to expand storage dynamically.

---

## Key Concepts

| Term | Description |
|------|-------------|
| **Volume** | A block storage device (like a hard drive) |
| **EBS** | AWS Elastic Block Storage — attach additional drives to EC2 |
| **Mount** | Binding a drive to a directory in the Linux file system |
| **Physical Volume (PV)** | A raw disk/partition used by LVM |
| **Volume Group (VG)** | A pool created by combining multiple physical volumes |
| **Logical Volume (LV)** | A flexible partition carved out of a volume group |
| **Snapshot** | A backup of a volume; can be used to create a new volume |

### What is Mount?
On EC2, the attached EBS drive is identified as `/dev/xvda`.  
Linux starts from the root directory `/`, and **mounting** means binding the drive to a specific path in the file system so it can be used.

---

## Essential Commands

| Command | Description |
|---------|-------------|
| `lsblk` | List all attached volumes (`xvda` = root volume) |
| `df -h` | Show free/used space with mount points |
| `lvm` | Enter the Logical Volume Manager CLI |
| `umount /path` | Unmount a volume |

---

## Workflow: Create & Attach an EBS Volume (AWS)

1. Go to **EC2 → EBS → Volumes** in the AWS console
2. Click **Create Volume** and configure size/type
3. **Attach** the new volume to your EC2 instance
4. Use `/dev/sdf` as the device name (letters a–e are reserved for root)
5. Verify with `lsblk` — the volume appears as `xvdf`

---

## LVM: Logical Volume Manager

Switch to root user first:
```bash
sudo su
```

### Step 1 — Create Physical Volumes
```bash
pvcreate /dev/xvdf /dev/xvdg /dev/xvdh
pvs          # verify physical volumes
pvdisplay    # detailed view
```

### Step 2 — Create a Volume Group
```bash
vgcreate tws-vg /dev/xvdf /dev/xvdg
vgs          # verify volume group
vgdisplay    # detailed view
```

### Step 3 — Create a Logical Volume
```bash
# Create a 10GB logical volume named tws_lv from tws_vg
lvcreate -L 10G -n tws_lv tws_vg
lvs          # verify logical volume
lvdisplay    # detailed view
```

### Step 4 — Mount the Logical Volume
```bash
# Create mount point directory
mkdir /mnt/tws_lv_mount

# Format the logical volume with ext4 filesystem
mkfs.ext4 /dev/tws_vg/tws_lv

# Mount it
mount /dev/tws_vg/tws_lv /mnt/tws_lv_mount

# Verify
df -h
```

---

## Direct Drive Mount (Without LVM)

```bash
# Create mount point
mkdir /mnt/disk_mount

# Format the drive with ext4
mkfs -t ext4 /dev/xvdh

# Mount it
mount /dev/xvdh /mnt/disk_mount

# Verify
df -h
```

---

## Extend a Logical Volume

```bash
# Add 5GB to an existing logical volume
lvextend -L 5G /dev/tws_vg/tws_lv
```

---

## Unmount

```bash
umount /mnt/tws_lv_mount
```
