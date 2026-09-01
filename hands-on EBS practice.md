# EBS Complete Hands-On Lab

## Architecture

```text
EC2 Instance
ap-south-1a
       |
       |--- Root Volume
       |      /dev/xvda
       |      /
       |
       |--- EBS Volume 1
       |      /dev/xvdf
       |      Partition
       |      /dev/xvdf1
       |      /data
       |
       |--- EBS Volume 2
       |      /dev/xvdg
       |      Manual Snapshot
       |
       |--- EBS Volume 3
       |      /dev/xvdh
       |      DLM Automatic Snapshot
       |
       |--- EBS Volume 4
              /dev/xvdi
              AWS Backup
```

> **All EBS volumes must be in the same Availability Zone as the EC2 instance for direct attachment.**

---

# PART 1 — Create 3 Additional EBS Volumes

## Step 1: Go to EC2 Volumes

```text
AWS Console
   ↓
EC2
   ↓
Volumes
   ↓
Create volume
```

Create the first volume:

```text
Volume Type → gp3
Size        → 10 GiB
AZ          → ap-south-1a
```

Create the second:

```text
Volume Type → gp3
Size        → 10 GiB
AZ          → ap-south-1a
```

Create the third:

```text
Volume Type → gp3
Size        → 10 GiB
AZ          → ap-south-1a
```

You now have:

```text
Volume 1 → 10 GB → 1a
Volume 2 → 10 GB → 1a
Volume 3 → 10 GB → 1a
```

---

# PART 2 — Attach All 3 Volumes

Go to:

```text
EC2
 ↓
Volumes
 ↓
Select Volume 1
 ↓
Actions
 ↓
Attach volume
```

Select your EC2 instance.

Device:

```text
/dev/xvdf
```

Repeat for Volume 2:

```text
/dev/xvdg
```

Repeat for Volume 3:

```text
/dev/xvdh
```

Final architecture:

```text
EC2
 |
 |--- /dev/xvda → Root
 |
 |--- /dev/xvdf → Volume 1
 |
 |--- /dev/xvdg → Volume 2
 |
 |--- /dev/xvdh → Volume 3
```

---

# PART 3 — Verify All Volumes

SSH into the EC2:

```bash
lsblk
```

Expected:

```text
NAME    SIZE TYPE MOUNTPOINT
xvda     20G disk
└─xvda1  20G part /
xvdf     10G disk
xvdg     10G disk
xvdh     10G disk
```

---

# PART 4 — Volume 1: Partition + Format + Mount

We will use Volume 1 for **partitioning practice**.

## Step 1: Create Partition

```bash
sudo fdisk /dev/xvdf
```

Inside `fdisk`:

```text
n → New partition
p → Primary
1 → Partition number
Enter → Default first sector
Enter → Use remaining space
w → Write changes
```

Check:

```bash
lsblk
```

You should see:

```text
xvdf
└─xvdf1
```

---

## Step 2: Check Filesystem

```bash
sudo file -s /dev/xvdf1
```

Since this is a new partition, it should not have a filesystem yet.

---

## Step 3: Format

```bash
sudo mkfs -t ext4 /dev/xvdf1
```

---

## Step 4: Create Mount Point

```bash
sudo mkdir /data
```

---

## Step 5: Mount

```bash
sudo mount /dev/xvdf1 /data
```

Verify:

```bash
df -h
```

---

## Step 6: Create Test Data

```bash
sudo touch /data/test.txt
sudo sh -c 'echo "EBS Test Data" > /data/test.txt'
```

Check:

```bash
cat /data/test.txt
```

---

# PART 5 — Persistent Mount Using fstab

Get UUID:

```bash
sudo blkid /dev/xvdf1
```

Example:

```text
/dev/xvdf1: UUID="1111-aaaa" TYPE="ext4"
```

Edit:

```bash
sudo vi /etc/fstab
```

Add:

```text
UUID=1111-aaaa /data ext4 defaults,nofail 0 2
```

Test:

```bash
sudo mount -a
```

Verify:

```bash
df -h
```

Now `/data` will automatically mount after reboot.

---

# PART 6 — Volume 2: Manual EBS Snapshot

We will use Volume 2 for a **manual backup**.

First format and mount it:

```bash
sudo mkfs -t ext4 /dev/xvdg
```

```bash
sudo mkdir /backup
```

```bash
sudo mount /dev/xvdg /backup
```

Create test data:

```bash
sudo sh -c 'echo "Manual Snapshot Data" > /backup/test.txt'
```

Verify:

```bash
cat /backup/test.txt
```

---

## Create Manual Snapshot

Go to:

```text
EC2
 ↓
Volumes
 ↓
Select Volume 2
 ↓
Actions
 ↓
Create snapshot
```

Description:

```text
Manual EBS Backup
```

Click:

```text
Create snapshot
```

Go to:

```text
EC2
 ↓
Snapshots
```

Wait for:

```text
Status → Completed
```

### Manual Backup Flow

```text
EBS Volume 2
     ↓
Create Snapshot
     ↓
Snapshot
     ↓
Backup
```

---

# PART 7 — Volume 3: DLM Automatic Backup

We will use Volume 3 for **DLM**.

First format and mount:

```bash
sudo mkfs -t ext4 /dev/xvdh
```

```bash
sudo mkdir /dlm-data
```

```bash
sudo mount /dev/xvdh /dlm-data
```

Create test data:

```bash
sudo sh -c 'echo "DLM Backup Data" > /dlm-data/test.txt'
```

---

## Step 1: Add Tag

Go to:

```text
EC2
 ↓
Volumes
 ↓
Select Volume 3
 ↓
Tags
 ↓
Manage tags
```

Add:

```text
Key   → Backup
Value → DLM
```

---

## Step 2: Create DLM Policy

Go to:

```text
EC2
 ↓
Lifecycle Manager
 ↓
Create lifecycle policy
```

Select:

```text
Policy type → EBS snapshot policy
```

Target:

```text
Resource type → Volume
```

Tag:

```text
Key   → Backup
Value → DLM
```

Schedule:

```text
Frequency → Daily
```

Retention:

```text
Keep → 7 snapshots
```

Create the policy.

### DLM Flow

```text
EBS Volume 3
     ↓
Tag: Backup=DLM
     ↓
DLM Policy
     ↓
Daily Snapshot
     ↓
Keep 7 Snapshots
     ↓
Old Snapshots Automatically Deleted
```

---

# PART 8 — AWS Backup

For this part, create/use another EBS volume if you want to keep the three-volume lab strictly separated.

Example:

```text
Volume 4
10 GB
ap-south-1a
```

Attach:

```text
/dev/xvdi
```

Check:

```bash
lsblk
```

Format:

```bash
sudo mkfs -t ext4 /dev/xvdi
```

Mount:

```bash
sudo mkdir /aws-backup
sudo mount /dev/xvdi /aws-backup
```

Create test data:

```bash
sudo sh -c 'echo "AWS Backup Data" > /aws-backup/test.txt'
```

---

# PART 9 — Create AWS Backup Plan

Go to:

```text
AWS Console
 ↓
AWS Backup
 ↓
Backup plans
 ↓
Create backup plan
```

Select:

```text
Create a new plan
```

Plan name:

```text
EBS-Daily-Backup
```

---

## Create Backup Rule

Example:

```text
Rule name    → Daily-EBS
Backup vault → Default
Frequency    → Daily
Retention   → 7 days
```

Create the plan.

---

# PART 10 — Assign EBS Volume

Inside the backup plan:

```text
Backup Plan
 ↓
Assign resources
```

You can use tags.

For Volume 4 add:

```text
Key   → Backup
Value → AWS
```

Then configure the resource selection:

```text
Resource type → EBS
Tag
Key   → Backup
Value → AWS
```

Save the assignment.

---

# PART 11 — Verify AWS Backup

Go to:

```text
AWS Backup
 ↓
Backup vaults
 ↓
Default
 ↓
Recovery points
```

You should eventually see the recovery point created by the backup plan.

### AWS Backup Flow

```text
EBS Volume 4
     ↓
Tag: Backup=AWS
     ↓
AWS Backup Plan
     ↓
Daily Backup
     ↓
Recovery Point
     ↓
Retention: 7 Days
```

---

# PART 12 — Restore EBS Backup

## Manual Snapshot Restore

```text
EC2
 ↓
Snapshots
 ↓
Select Snapshot
 ↓
Actions
 ↓
Create volume from snapshot
```

Choose:

```text
Availability Zone → ap-south-1a
```

Create the volume.

Then:

```text
New EBS
   ↓
Attach to EC2
   ↓
lsblk
   ↓
Mount
```

---

## DLM Restore

```text
DLM Snapshot
     ↓
Select Snapshot
     ↓
Create Volume
     ↓
Select AZ
     ↓
Attach to EC2
     ↓
Mount
```

---

## AWS Backup Restore

```text
AWS Backup
     ↓
Backup Vault
     ↓
Recovery Point
     ↓
Restore
     ↓
New EBS Volume
     ↓
Attach to EC2
     ↓
Mount
```

---

# Final Architecture

```text
                    EC2
                 ap-south-1a
                     |
        +------------+------------+
        |            |            |
      Root         EBS 1        EBS 2
     /dev/xvda   /dev/xvdf     /dev/xvdg
        |            |            |
        /          /data       Manual
                                  Snapshot
                     |
                    EBS 3
                  /dev/xvdh
                     |
                  DLM Backup
                     |
                    EBS 4
                  /dev/xvdi
                     |
                AWS Backup
```

# What You Learn

```text
1. Create EBS
2. Attach EBS to EC2
3. Check with lsblk
4. Partition EBS
5. Format filesystem
6. Mount EBS
7. Configure /etc/fstab
8. Take manual snapshot
9. Configure DLM
10. Configure AWS Backup
11. Restore from snapshot
12. Restore from DLM
13. Restore from AWS Backup
```

# Interview Summary

> **"I have worked with EBS by creating and attaching multiple volumes to an EC2 instance. I partitioned and formatted a volume, mounted it, and configured persistent mounting using `/etc/fstab`. For backup, I practiced three approaches: manual EBS snapshots, automated snapshots using Data Lifecycle Manager, and centralized automated backups using AWS Backup. I also tested restoring the backups by creating new EBS volumes and attaching them to EC2."**
