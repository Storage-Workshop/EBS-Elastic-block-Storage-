## EBS Snapshot

### What is an EBS Snapshot?

* An **EBS Snapshot** is a point-in-time backup of an EBS volume.
* It is mainly used for **backup, recovery, and disaster recovery**.
* Snapshots are stored and managed by AWS.
* We can create a new EBS volume from a snapshot.
* A snapshot can be used to create a volume in another **Availability Zone**.

### Snapshot Flow

```text
EBS Volume
     ↓
Create Snapshot
     ↓
Snapshot
     ↓
Create EBS Volume
     ↓
Select Availability Zone
     ↓
Attach to EC2
```

### Example

```text
Existing EBS
ap-south-1a
     ↓
   Snapshot
     ↓
New EBS
ap-south-1c
     ↓
New EC2
ap-south-1c
```

This is useful when the **existing EBS and new EC2 are in different Availability Zones**.

### Create Snapshot from AWS Console

```text
EC2
 ↓
Volumes
 ↓
Select EBS Volume
 ↓
Actions
 ↓
Create Snapshot
 ↓
Enter Description
 ↓
Create Snapshot
```

### Create Volume from Snapshot

```text
EC2
 ↓
Snapshots
 ↓
Select Snapshot
 ↓
Actions
 ↓
Create Volume from Snapshot
 ↓
Select Availability Zone
 ↓
Create Volume
```

### Important Points

* Snapshot is a **backup of an EBS volume**.
* Snapshot is useful for **data recovery**.
* Snapshot can be used to create a new EBS volume.
* The new EBS volume can be created in a different AZ.
* We can use snapshots to migrate data between AZs.
* We can use snapshots to restore an EC2 server's data.
* Snapshot is different from an EBS volume: **EBS is the active storage; snapshot is the backup/copy.**

### Interview Answer

> **"An EBS snapshot is a point-in-time backup of an EBS volume. We use snapshots for backup and recovery. If we need to use the data in another Availability Zone, we can create a new EBS volume from the snapshot in the required AZ and attach it to an EC2 instance."**
