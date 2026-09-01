## Create EBS Snapshot and Change Availability Zone

### Scenario

Suppose:

```text
Existing EBS Volume → ap-south-1a
New EC2 Instance    → ap-south-1c
```

We **cannot directly attach** the EBS volume from `1a` to the EC2 in `1c`.

We use an **EBS Snapshot** to create a new volume in `1c`.

---

### Step 1: Create Snapshot

Go to:

```text
AWS Console
   ↓
EC2
   ↓
Volumes
   ↓
Select Existing EBS Volume
   ↓
Actions
   ↓
Create Snapshot
```

Enter a description:

```text
EBS Backup - Application Data
```

Click:

```text
Create Snapshot
```

---

### Step 2: Go to Snapshots

```text
EC2
   ↓
Snapshots
```

Find the snapshot you just created.

Wait until the snapshot status becomes:

```text
Completed
```

---

### Step 3: Create New EBS Volume from Snapshot

Select the snapshot:

```text
Actions
   ↓
Create volume from snapshot
```

Configure:

```text
Availability Zone → ap-south-1c
Volume Type       → gp3
```

Then click:

```text
Create volume
```

Now:

```text
Original EBS
ap-south-1a
      ↓
   Snapshot
      ↓
New EBS
ap-south-1c
```

---

### Step 4: Attach New EBS to EC2

Go to:

```text
EC2
   ↓
Volumes
   ↓
Select New EBS
   ↓
Actions
   ↓
Attach volume
```

Select the EC2 instance in:

```text
ap-south-1c
```

Device:

```text
/dev/xvdf
```

Click:

```text
Attach volume
```

---

### Step 5: Verify From EC2

Connect to the new EC2 instance:

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

Check the filesystem:

```bash
sudo file -s /dev/xvdf
```

If it shows:

```text
/dev/xvdf: Linux rev 1.0 ext4 filesystem data
```

the filesystem exists.

---

### Step 6: Mount the Volume

Create mount point:

```bash
sudo mkdir /data
```

Mount:

```bash
sudo mount /dev/xvdf /data
```

Verify:

```bash
df -h
```

Check the data:

```bash
ls -la /data
```

---

## Complete Flow

```text
EBS Volume
ap-south-1a
      ↓
Create Snapshot
      ↓
Snapshot
      ↓
Create Volume from Snapshot
      ↓
Select AZ: ap-south-1c
      ↓
New EBS Volume
ap-south-1c
      ↓
Attach to EC2
ap-south-1c
      ↓
lsblk
      ↓
file -s /dev/xvdf
      ↓
mount /dev/xvdf /data
      ↓
Access Data
```

## Important Interview Point

> **EBS volumes are AZ-specific. If an EBS volume is in `ap-south-1a` and the EC2 instance is in `ap-south-1c`, we cannot directly attach the volume. We can create a snapshot of the original EBS volume, create a new EBS volume from that snapshot in `ap-south-1c`, and then attach the new volume to the EC2 instance.**
