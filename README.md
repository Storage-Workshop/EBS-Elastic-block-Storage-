# AWS EBS (Elastic Block Store)

## What is EBS?

**Amazon EBS (Elastic Block Store)** is a block-level storage service provided by AWS.

We mainly use EBS volumes as storage for EC2 instances. The data stored in an EBS volume persists independently from the EC2 instance, depending on the volume's `DeleteOnTermination` setting.

### Simple Example

```text
                AWS
                 |
              EC2 Instance
                 |
        -------------------
        |                 |
    Root EBS          Data EBS
    /dev/xvda         /dev/xvdb
        |                 |
    OS files         Application data
```

---

## Key Features of EBS

* Block-level storage for EC2
* Persistent storage
* Can be attached to and detached from EC2
* Supports snapshots
* Can increase volume size
* Supports encryption
* Different volume types are available
* EBS volumes are associated with a specific Availability Zone

---

## Common EBS Volume Types

### 1. gp3

General-purpose SSD.

Used for:

* Applications
* Web servers
* Databases
* General workloads

Example:

```text
gp3 → General purpose SSD
```

### 2. gp2

Previous-generation general-purpose SSD.

```text
gp2 → General purpose SSD
```

### 3. io2

Provisioned IOPS SSD.

Used when we need:

* High IOPS
* High performance
* Mission-critical databases

```text
io2 → High IOPS / high performance
```

### 4. st1

Throughput Optimized HDD.

Used for:

* Big data
* Data warehouses
* Log processing

### 5. sc1

Cold HDD.

Used for:

* Infrequently accessed data
* Low-cost storage

---

# EBS vs Instance Store

| EBS                                                    | Instance Store                                                          |
| ------------------------------------------------------ | ----------------------------------------------------------------------- |
| Persistent storage                                     | Temporary storage                                                       |
| Can survive EC2 termination depending on settings      | Data is lost when instance is stopped/terminated depending on lifecycle |
| Supports snapshots                                     | No EBS-style snapshots                                                  |
| Network-attached block storage                         | Physically attached to host                                             |
| Can be detached and attached to another compatible EC2 | Cannot be detached like EBS                                             |

---

# Attaching an EBS Volume to EC2

Example:

```text
EC2 Instance
     |
     +---- /dev/xvda → Root EBS
     |
     +---- /dev/xvdb → Additional EBS
```

After attaching the volume, connect to the EC2 instance and check:

```bash
lsblk
```

Example:

```text
NAME    MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
xvda      8:0    0   20G  0 disk
└─xvda1   8:1    0   20G  0 part /
xvdb      8:16   0   50G  0 disk
```

---

# Formatting the EBS Volume

For an unused volume:

```bash
sudo mkfs -t ext4 /dev/xvdb
```

> Be careful: `mkfs` formats the disk and can destroy existing data.

---

# Creating a Mount Point

```bash
sudo mkdir /data
```

Mount the volume:

```bash
sudo mount /dev/xvdb /data
```

Verify:

```bash
df -h
```

Now `/dev/xvdb` is mounted on `/data`.

---

# Making the Mount Persistent

Get the UUID:

```bash
sudo blkid /dev/xvdb
```

Example:

```text
UUID="1234-abcd-5678"
```

Add it to `/etc/fstab`:

```text
UUID=1234-abcd-5678 /data ext4 defaults,nofail 0 2
```

Then test:

```bash
sudo mount -a
```

If there is no error, the configuration is valid.

---

# What Happens When EC2 Is Terminated?

It depends on the `DeleteOnTermination` setting.

### Root EBS

Normally:

```text
EC2 Terminated
      ↓
Root EBS Deleted
```

### Additional Data EBS

If:

```text
DeleteOnTermination = False
```

then:

```text
EC2 Terminated
      ↓
EBS remains
      ↓
Can attach to another EC2
```

This is commonly used when we want application data to survive EC2 replacement.

---

# EBS Snapshot

An **EBS snapshot** is a point-in-time backup of an EBS volume.

Example:

```text
EBS Volume
    |
    ↓
Snapshot
    |
    ↓
New EBS Volume
```

Snapshots are stored in Amazon S3 behind the scenes and are useful for backup and disaster recovery.

---

# Important Interview Question

### What is EBS?

> EBS stands for Elastic Block Store. It is a persistent block storage service used with EC2 instances. We can create EBS volumes, attach them to EC2 instances, format and mount them, and take snapshots for backup and recovery.

### What happens to EBS when EC2 is terminated?

> It depends on the DeleteOnTermination attribute. The root EBS volume is usually deleted when the EC2 instance is terminated, while additional data volumes can be configured to remain after termination.

### Can we attach an EBS volume to another EC2?

> Yes, we can detach an EBS volume from one EC2 instance and attach it to another compatible EC2 instance, subject to AWS attachment and Availability Zone constraints.

### EBS vs EFS?

> EBS is block storage and is primarily used with EC2. EFS is a managed network file system that can be mounted by multiple compute instances.

---

# Useful AWS CLI Commands

List EBS volumes:

```bash
aws ec2 describe-volumes
```

Create an EBS volume:

```bash
aws ec2 create-volume \
  --availability-zone ap-south-1a \
  --size 20 \
  --volume-type gp3
```

Attach a volume:

```bash
aws ec2 attach-volume \
  --volume-id vol-xxxxxxxx \
  --instance-id i-xxxxxxxx \
  --device /dev/xvdb
```

Detach a volume:

```bash
aws ec2 detach-volume \
  --volume-id vol-xxxxxxxx
```

Create a snapshot:

```bash
aws ec2 create-snapshot \
  --volume-id vol-xxxxxxxx \
  --description "EBS Backup"
```

---

# Real-World DevOps Example

Suppose we have an EC2 application server:

```text
                 EC2
                  |
        --------------------
        |                  |
    Root Volume        Data Volume
     20 GB              100 GB
        |                  |
       OS            Application Data
```

If the EC2 instance fails, we can:

1. Launch a new EC2 instance.
2. Detach the data EBS volume from the old instance if necessary.
3. Attach the data volume to the new instance.
4. Mount the volume.
5. Start the application.

This allows application data to be preserved even when the EC2 instance is replaced.

---

# Important Points to Remember

```text
EBS
 |
 +-- Block storage
 |
 +-- Mainly used with EC2
 |
 +-- Persistent
 |
 +-- Can attach/detach
 |
 +-- Supports snapshots
 |
 +-- Supports encryption
 |
 +-- Volume belongs to an Availability Zone
 |
 +-- DeleteOnTermination controls deletion with EC2
```

## Interview One-Liner

> **"EBS is persistent block storage for EC2. We use it for OS and application data, and we can attach, detach, resize, encrypt, and create snapshots of EBS volumes. The DeleteOnTermination setting determines whether a volume is deleted when its EC2 instance is terminated."**
