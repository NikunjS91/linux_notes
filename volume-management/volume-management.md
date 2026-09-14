# Linux Volume Management

---

## Overview

In Linux (on AWS EC2), storage is organized in **blocks**. The disk that an instance boots from is its **root volume**, but you can attach additional block storage devices to expand capacity.

AWS provides this through **EBS (Elastic Block Storage)** — you can create, attach, resize, and snapshot volumes independently of your EC2 instance.

---

## Key Concepts

| Term | Description |
|------|-------------|
| **Block Device** | A storage device accessed in fixed-size blocks (e.g., `/dev/xvda`) |
| **Volume** | A block storage device — like a virtual hard drive |
| **EBS** | AWS Elastic Block Storage — attach extra volumes to EC2 instances |
| **Mount** | Binding a storage device to a directory path in Linux so it's usable |
| **Filesystem** | Structure that organizes data on a volume (e.g., ext4, xfs) |
| **Physical Volume (PV)** | A raw disk or partition registered with LVM |
| **Volume Group (VG)** | A storage pool built by combining one or more physical volumes |
| **Logical Volume (LV)** | A flexible, resizable partition carved out of a volume group |
| **Snapshot** | Point-in-time backup of a volume — can restore or create a new volume from it |

---

## Device Naming on EC2

| Device Name | Meaning |
|-------------|---------|
| `/dev/xvda` | Root (boot) volume — do not modify |
| `/dev/xvdb` to `/dev/xvde` | Reserved — avoid using |
| `/dev/xvdf` onwards | Safe to use for additional volumes |

When you attach an EBS volume as `/dev/sdf`, Linux shows it as `/dev/xvdf`. The `x` prefix is added by the Xen hypervisor used by EC2.

---

## Essential Commands

| Command | Description |
|---------|-------------|
| `lsblk` | List all block devices and their mount points in a tree view |
| `lsblk -f` | Also show filesystem type and UUID for each device |
| `df -h` | Show disk space usage and mount points |
| `blkid` | Show UUID and filesystem type of all devices |
| `fdisk -l` | List partitions on all disks (requires root) |
| `mount` | Show all currently mounted filesystems |
| `umount /path` | Unmount a device from a directory |

---

## Workflow: Attach an EBS Volume on AWS

### EBS Volume Types

| Type | Name | Use Case | Max IOPS |
|------|------|----------|----------|
| `gp3` | General Purpose SSD v3 | Most workloads — best price/performance | 16,000 |
| `gp2` | General Purpose SSD v2 | Older default — IOPS tied to size | 16,000 |
| `io1` / `io2` | Provisioned IOPS SSD | High-performance databases | 64,000+ |
| `st1` | Throughput Optimized HDD | Big data, log processing (sequential reads) | 500 |
| `sc1` | Cold HDD | Infrequent access, lowest cost | 250 |

> **Recommendation:** Use `gp3` for new volumes — it's cheaper than `gp2` and lets you set IOPS independently.

### Step 1 — Create the Volume
1. Go to **AWS Console → EC2 → Elastic Block Store → Volumes**
2. Click **Create Volume**
3. Choose: size, type (gp3 recommended), and same Availability Zone as your EC2 instance
4. Optionally create from a **Snapshot** (backup)

### Step 2 — Attach to Your Instance
1. Select the new volume → **Actions → Attach Volume**
2. Choose your EC2 instance
3. Set device name to `/dev/sdf` (Linux will show it as `/dev/xvdf`)

### Step 3 — Verify in Linux
```bash
lsblk
# xvdf should now appear in the output
```

---

## Filesystem Types

| Filesystem | Best For | Max File Size | Notes |
|------------|----------|---------------|-------|
| `ext4` | General purpose Linux | 16 TB | Default on most Linux distros, stable and well-supported |
| `xfs` | Large files, high performance | 8 EB | Default on RHEL/CentOS, great for big data |
| `ext3` | Legacy systems | 2 TB | Older ext4 — avoid for new volumes |
| `btrfs` | Snapshots, RAID features | 16 EB | Modern, but less battle-tested than ext4/xfs |
| `tmpfs` | Temporary in-memory storage | RAM-limited | Lives in RAM — data lost on reboot |

> For most EC2 workloads, use **ext4** (general use) or **xfs** (heavy throughput).

---

## Option A: Direct Mount (Without LVM)

Use this when you have a single volume and don't need flexibility.

```bash
# 1. Switch to root
sudo su

# 2. Create a directory to mount the volume to
mkdir /mnt/disk_mount

# 3. Format the volume with ext4 filesystem
#    WARNING: this erases all existing data on the volume
mkfs -t ext4 /dev/xvdh

# 4. Mount the volume
mount /dev/xvdh /mnt/disk_mount

# 5. Verify
df -h
```

---

## Option B: LVM — Logical Volume Manager

Use LVM when you want to combine multiple disks, resize volumes easily, or manage storage more flexibly.

### LVM Architecture

```
Physical Volumes (PV)       /dev/xvdf  /dev/xvdg  /dev/xvdh
         |
         ▼
Volume Group (VG)           tws-vg  (combined pool)
         |
         ▼
Logical Volumes (LV)        tws_lv  (10GB)  tws_lv2  (5GB)  ...
```

---

### Step 1 — Enter LVM and Create Physical Volumes

```bash
# Switch to root
sudo su

# Enter LVM shell (optional interactive mode)
lvm

# Create physical volumes from block devices
pvcreate /dev/xvdf /dev/xvdg /dev/xvdh

# Verify
pvs            # summary
pvdisplay      # detailed view
```

---

### Step 2 — Create a Volume Group

```bash
# Combine two physical volumes into a group named "tws-vg"
vgcreate tws-vg /dev/xvdf /dev/xvdg

# Verify
vgs            # summary
vgdisplay      # detailed view
```

---

### Step 3 — Create a Logical Volume

```bash
# Create a 10GB logical volume named "tws_lv" from group "tws_vg"
lvcreate -L 10G -n tws_lv tws_vg

# Verify
lvs            # summary
lvdisplay      # detailed view
```

---

### Step 4 — Format and Mount

```bash
# Create the mount point directory
mkdir /mnt/tws_lv_mount

# Format with ext4 filesystem
mkfs.ext4 /dev/tws_vg/tws_lv

# Mount it
mount /dev/tws_vg/tws_lv /mnt/tws_lv_mount

# Verify — should appear in the list
df -h
```

---

## Extend a Logical Volume (Resize)

One of LVM's biggest advantages — resize without unmounting (on most filesystems).

```bash
# Extend logical volume by an additional 5GB
lvextend -L +5G /dev/tws_vg/tws_lv

# Resize the filesystem to use the new space (ext4)
resize2fs /dev/tws_vg/tws_lv

# Verify new size
df -h
```

> Use `+5G` (with the `+`) to add 5GB to the current size. Without `+`, it sets the total size to 5GB.

---

## Add a New Physical Volume to an Existing Group

When your volume group is running out of space, add another disk:

```bash
# Attach a new EBS volume (e.g., /dev/xvdi) first via AWS console

# Register it as a physical volume
pvcreate /dev/xvdi

# Add it to the existing volume group
vgextend tws-vg /dev/xvdi

# Now extend a logical volume using the new space
lvextend -L +10G /dev/tws_vg/tws_lv
resize2fs /dev/tws_vg/tws_lv
```

---

## Unmount a Volume

```bash
umount /mnt/tws_lv_mount

# If "device is busy" error:
# Find what process is using it
fuser -m /mnt/tws_lv_mount

# Kill the process using it
fuser -km /mnt/tws_lv_mount
```

---

## Make Mount Persistent (Survive Reboots)

By default, mounts are lost after a reboot. To make them permanent, add an entry to `/etc/fstab`.

```bash
# Find the UUID of your volume
blkid /dev/tws_vg/tws_lv

# Edit /etc/fstab and add a line:
# UUID=your-uuid  /mnt/tws_lv_mount  ext4  defaults  0  2

# Test without rebooting
mount -a
df -h
```

### /etc/fstab Fields Explained

Each line in `/etc/fstab` has 6 space-separated fields:

```
UUID=abc123  /mnt/data  ext4  defaults  0  2
```

| Field | Example | Meaning |
|-------|---------|---------|
| 1 — Device | `UUID=abc123` or `/dev/xvdf` | What to mount (UUID is preferred — stable across reboots) |
| 2 — Mount point | `/mnt/data` | Where in the filesystem to mount it |
| 3 — Filesystem type | `ext4` | Type of filesystem on the volume |
| 4 — Options | `defaults` | Mount options (see below) |
| 5 — Dump | `0` | Backup tool flag — almost always `0` |
| 6 — fsck order | `2` | Filesystem check order at boot (`0`=skip, `1`=root, `2`=others) |

**Common mount options:**

| Option | Meaning |
|--------|---------|
| `defaults` | Standard options: rw, suid, exec, auto, nouser, async |
| `ro` | Mount read-only |
| `rw` | Mount read-write |
| `noexec` | Prevent executing binaries from this volume |
| `noatime` | Don't update access time on reads (improves performance) |
| `nofail` | Don't fail boot if this device is missing |

---

## LVM Quick Reference

| Command | Description |
|---------|-------------|
| `pvcreate /dev/xvdf` | Create a physical volume |
| `pvs` | List physical volumes |
| `pvdisplay` | Detailed physical volume info |
| `pvremove /dev/xvdf` | Remove a physical volume |
| `vgcreate name /dev/xvdf` | Create a volume group |
| `vgs` | List volume groups |
| `vgdisplay` | Detailed volume group info |
| `vgextend name /dev/xvdi` | Add a disk to a volume group |
| `lvcreate -L 10G -n name vg` | Create a logical volume |
| `lvs` | List logical volumes |
| `lvdisplay` | Detailed logical volume info |
| `lvextend -L +5G /dev/vg/lv` | Grow a logical volume |
| `resize2fs /dev/vg/lv` | Resize ext4 filesystem after lvextend |
| `lvremove /dev/vg/lv` | Delete a logical volume |
