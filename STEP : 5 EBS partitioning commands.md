## EBS Partitioning Commands

### 1. Check Attached EBS Volume

```bash
lsblk
```

Example:

```text
NAME    SIZE TYPE MOUNTPOINT
xvda     20G disk
└─xvda1  20G part /
xvdf     50G disk
```

Here `/dev/xvdf` is the new EBS disk.

---

### 2. Check Existing Filesystem

```bash
sudo file -s /dev/xvdf
```

> If this is an existing EBS volume containing data, **do not partition or format it without confirming the data requirements**.

---

### 3. Create Partition Using fdisk

```bash
sudo fdisk /dev/xvdf
```

Inside `fdisk`:

```text
n     → Create new partition
p     → Primary partition
1     → Partition number
Enter → Accept default first sector
Enter → Use remaining disk space
w     → Write changes and exit
```

Then check:

```bash
lsblk
```

You should see:

```text
xvdf
└─xvdf1
```

---

### 4. Ask Linux to Re-read Partition Table

Usually `fdisk` handles this automatically. If needed:

```bash
sudo partprobe /dev/xvdf
```

Then:

```bash
lsblk
```

---

### 5. Create Filesystem

For `ext4`:

```bash
sudo mkfs -t ext4 /dev/xvdf1
```

For `xfs`:

```bash
sudo mkfs.xfs /dev/xvdf1
```

> ⚠️ `mkfs` formats the partition. Do not run it on a partition containing important existing data.

---

### 6. Create Mount Point

```bash
sudo mkdir /data
```

---

### 7. Mount the Partition

```bash
sudo mount /dev/xvdf1 /data
```

Verify:

```bash
df -h
```

---

### 8. Check the Partition

```bash
lsblk
```

Example:

```text
NAME    SIZE TYPE MOUNTPOINT
xvda     20G disk
└─xvda1  20G part /
xvdf     50G disk
└─xvdf1  50G part /data
```

---

### 9. Get UUID

```bash
sudo blkid /dev/xvdf1
```

Example:

```text
/dev/xvdf1: UUID="abcd-1234" TYPE="ext4"
```

---

### 10. Permanent Mount Using /etc/fstab

Edit:

```bash
sudo vi /etc/fstab
```

Add:

```text
UUID=abcd-1234 /data ext4 defaults,nofail 0 2
```

Test:

```bash
sudo mount -a
```

Verify:

```bash
df -h
```

---

## Complete Flow

```text
EBS Volume
    ↓
lsblk
    ↓
/dev/xvdf
    ↓
fdisk /dev/xvdf
    ↓
/dev/xvdf1
    ↓
mkfs -t ext4 /dev/xvdf1
    ↓
mkdir /data
    ↓
mount /dev/xvdf1 /data
    ↓
df -h
    ↓
blkid
    ↓
/etc/fstab
```

## Important Commands

```bash
lsblk
sudo fdisk /dev/xvdf
sudo partprobe /dev/xvdf
sudo mkfs -t ext4 /dev/xvdf1
sudo mkdir /data
sudo mount /dev/xvdf1 /data
df -h
sudo blkid /dev/xvdf1
sudo mount -a
```

### Easy Rule

> **Disk → Partition → Filesystem → Mount Point**

```text
/dev/xvdf
    ↓
/dev/xvdf1
    ↓
ext4
    ↓
/data
```
