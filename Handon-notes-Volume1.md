# PART 4 — Volume 1: Partition + Format + Mount

We will use **Volume 1** for partitioning practice.

```text
EBS Volume 1
/dev/xvdf
     ↓
Partition
/dev/xvdf1
     ↓
Filesystem
ext4
     ↓
Mount Point
/data
```

## Step 1: Check the Volume

```bash
lsblk
```

Example:

```text
NAME    SIZE TYPE MOUNTPOINT
xvda     20G disk
└─xvda1  20G part /
xvdf     10G disk
```

Here:

```text
/dev/xvdf → Additional EBS Volume
```

## Step 2: Create Partition

Run:

```bash
sudo fdisk /dev/xvdf
```

Inside `fdisk`:

```text
n → Create new partition
p → Primary partition
1 → Partition number
Enter → Default first sector
Enter → Use remaining space
w → Write changes and exit
```

Check:

```bash
lsblk
```

Expected:

```text
xvdf
└─xvdf1
```

Now:

```text
/dev/xvdf  → EBS disk
/dev/xvdf1 → Partition
```

## Step 3: Check Filesystem

```bash
sudo file -s /dev/xvdf1
```

Since this is a new partition, it should not have a filesystem yet.

## Step 4: Format the Partition

Create an `ext4` filesystem:

```bash
sudo mkfs -t ext4 /dev/xvdf1
```

Now:

```text
/dev/xvdf
    ↓
/dev/xvdf1
    ↓
ext4
```

## Step 5: Create Mount Point

```bash
sudo mkdir /data
```

## Step 6: Mount the Partition

```bash
sudo mount /dev/xvdf1 /data
```

Verify:

```bash
df -h
```

Expected:

```text
Filesystem      Size  Mounted on
/dev/xvda1       20G  /
/dev/xvdf1       10G  /data
```

You can also check:

```bash
lsblk
```

## Step 7: Create Test Data

```bash
sudo sh -c 'echo "EBS Test Data" > /data/test.txt'
```

Check:

```bash
cat /data/test.txt
```

Output:

```text
EBS Test Data
```

## Complete Flow

```text
EBS Volume
/dev/xvdf
    ↓
fdisk
    ↓
Partition
/dev/xvdf1
    ↓
mkfs -t ext4
    ↓
Filesystem
ext4
    ↓
mkdir /data
    ↓
mount
    ↓
/data
```

## Commands in One Place

```bash
lsblk

sudo fdisk /dev/xvdf

sudo partprobe /dev/xvdf

lsblk

sudo file -s /dev/xvdf1

sudo mkfs -t ext4 /dev/xvdf1

sudo mkdir /data

sudo mount /dev/xvdf1 /data

df -h

lsblk

sudo sh -c 'echo "EBS Test Data" > /data/test.txt'

cat /data/test.txt
```

## Important Note

> ⚠️ **Do not run `mkfs` on an existing EBS volume or partition that contains important data. Formatting can destroy the existing filesystem and data.**

<img width="584" height="330" alt="image" src="https://github.com/user-attachments/assets/19fe0dfe-fa16-4c48-95cb-c0a8fb4e3fd5" />

<img width="614" height="266" alt="image" src="https://github.com/user-attachments/assets/67a01bc4-f9f5-4db5-8ecb-d56fe15409ab" />

<img width="350" height="159" alt="image" src="https://github.com/user-attachments/assets/d8c2efe8-806a-4c2a-99e6-256d6d89a43b" />





### Interview Answer

> **"First I identify the attached EBS volume using `lsblk`. Then, if it is a new volume, I create a partition using `fdisk`, create an ext4 filesystem using `mkfs`, create a mount point such as `/data`, and mount the partition. Finally, I verify the mount using `df -h` and `lsblk`."**
