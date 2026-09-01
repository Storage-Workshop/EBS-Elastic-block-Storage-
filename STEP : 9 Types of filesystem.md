# Linux File Systems and AWS EBS Volumes

## 1. What is a File System?

A **file system** is a method used by an operating system to organize, store, and manage data on a storage device such as:

* Hard Disk
* SSD
* AWS EBS Volume
* USB Drive
* Local Disk

A file system allows the operating system to store files and directories and keep track of where the data is located.

### Simple Example

An EBS volume is like an **empty cupboard**.

The file system decides **how the cupboard is organized** so Linux can store and find files.

```text
EBS Volume
    ↓
File System
    ↓
Directories
    ↓
Files
```

---

# 2. Why Do We Need a File System?

Without a file system, Linux cannot normally use a raw disk/volume to store files and directories.

A file system provides:

* File and directory organization
* File permissions
* Ownership
* Metadata
* Storage management
* File access
* Recovery mechanisms

For example:

```text
/data
 ├── application
 ├── logs
 ├── backups
 └── database
```

The file system manages how these files are stored on the underlying disk.

---

# 3. Important Linux File Systems

The most commonly used Linux file systems are:

| File System | Common Usage                               |
| ----------- | ------------------------------------------ |
| ext4        | General Linux workloads                    |
| XFS         | Enterprise servers, large files, databases |
| Btrfs       | Advanced Linux storage features            |
| ext3        | Older Linux systems                        |
| tmpfs       | Temporary data stored in RAM               |
| NFS         | Network/shared file system                 |
| EFS         | AWS managed shared file system             |

For AWS EC2, the most important choices are generally:

```text
ext4
XFS
```

---

# 4. ext4

`ext4` is one of the most commonly used Linux file systems.

### Advantages

* Stable
* Mature
* Good general-purpose performance
* Supported by most Linux distributions
* Easy to manage
* Good choice for general applications

### Example

If you have an EC2 instance running an application:

```text
EC2
 |
 +-- /dev/xvdf
       |
       +-- ext4
             |
             +-- /data
```

You can format the volume:

```bash
sudo mkfs.ext4 /dev/xvdf
```

Then mount it:

```bash
sudo mkdir /data
sudo mount /dev/xvdf /data
```

---

# 5. XFS

`XFS` is a high-performance journaling file system commonly used in enterprise Linux environments.

It is particularly useful for:

* Large files
* Large storage volumes
* High-performance workloads
* Enterprise applications
* Database workloads
* Log storage

Format an EBS volume with XFS:

```bash
sudo mkfs.xfs /dev/xvdf
```

Mount it:

```bash
sudo mkdir /data
sudo mount /dev/xvdf /data
```

Check the file system:

```bash
df -Th
```

Example:

```text
Filesystem     Type   Size  Used  Avail  Use%  Mounted on
/dev/xvdf      xfs    100G  10G   90G    10%   /data
```

---

# 6. ext4 vs XFS

| Feature              | ext4        | XFS           |
| -------------------- | ----------- | ------------- |
| General-purpose      | Excellent   | Excellent     |
| Stability            | Excellent   | Excellent     |
| Large files          | Good        | Excellent     |
| Large volumes        | Good        | Excellent     |
| Enterprise workloads | Good        | Excellent     |
| Easy to manage       | Yes         | Yes           |
| Common on Linux      | Very common | Very common   |
| Shrinking filesystem | Supported   | Not supported |
| Growing filesystem   | Supported   | Supported     |

### Simple Decision

```text
General Linux application
        ↓
      ext4

Large files / enterprise workload
        ↓
       XFS
```

---

# 7. What is a Raw EBS Volume?

When you create an AWS EBS volume, the volume is initially just **block storage**.

Example:

```text
AWS EBS
   |
   ↓
/dev/xvdf
```

Linux can see the disk, but it does not automatically mean that the disk has a usable file system.

You normally do:

```text
EBS Volume
     ↓
Create File System
     ↓
Mount
     ↓
Use for storing files
```

---

# 8. Example: Attach EBS Volume to EC2

Suppose you create:

```text
EBS Volume
Size: 100 GB
```

Attach it to:

```text
EC2 Instance
```

Check disks:

```bash
lsblk
```

Example:

```text
NAME    SIZE TYPE
xvda     20G disk
└─xvda1  20G part
xvdf    100G disk
```

Here:

```text
xvda = Root volume
xvdf = Additional EBS volume
```

---

# 9. Create a File System

For ext4:

```bash
sudo mkfs.ext4 /dev/xvdf
```

For XFS:

```bash
sudo mkfs.xfs /dev/xvdf
```

**Important:** Do not run `mkfs` on a volume containing important data because formatting can destroy existing data.

---

# 10. Create Mount Point

Create a directory:

```bash
sudo mkdir /data
```

Mount the volume:

```bash
sudo mount /dev/xvdf /data
```

Verify:

```bash
df -h
```

Example:

```text
Filesystem      Size  Used Avail Use% Mounted on
/dev/xvdf      100G  1G   99G    1% /data
```

Now you can store data:

```bash
cd /data

touch test.txt
mkdir application
mkdir logs
```

---

# 11. Why Do We Mount a File System?

Mounting connects the file system to a directory in the Linux directory structure.

For example:

```text
EBS Volume
    |
    ↓
XFS
    |
    ↓
/data
```

Anything stored under:

```text
/data
```

will be stored on that EBS volume.

Example:

```text
/data
 ├── application
 ├── logs
 ├── uploads
 └── backups
```

---

# 12. Persistent Mount Using /etc/fstab

If you manually mount:

```bash
sudo mount /dev/xvdf /data
```

the mount may not automatically survive a reboot.

For persistent mounting, add the volume to:

```text
/etc/fstab
```

First find the UUID:

```bash
sudo blkid /dev/xvdf
```

Example:

```text
UUID="1234-abcd-5678"
TYPE="xfs"
```

Add:

```text
UUID=1234-abcd-5678 /data xfs defaults,nofail 0 2
```

Then test:

```bash
sudo mount -a
```

If there is no error, the configuration is generally valid.

---

# 13. Which File System Should We Choose for AWS EBS?

### Scenario 1: Normal Application Data

Use:

```text
ext4
```

Example:

```text
Application files
Configuration files
General data
```

---

### Scenario 2: Enterprise / Large Files

Use:

```text
XFS
```

Example:

```text
Large application data
Large log files
Enterprise workloads
```

---

### Scenario 3: Database

The correct choice depends on the database and workload.

Common choices include:

```text
XFS
ext4
```

You should also consider:

* IOPS
* Throughput
* Latency
* Database requirements
* Backup strategy
* EBS volume type

---

# 14. EBS Volume Type vs File System

These are **two different things**.

Do not confuse them.

### EBS Volume Type

Defines the underlying AWS block-storage characteristics.

Examples:

```text
gp3
io2
st1
sc1
```

### File System

Defines how Linux organizes data on the volume.

Examples:

```text
ext4
XFS
```

Therefore:

```text
EC2
 |
 +-- EBS gp3
       |
       +-- XFS
             |
             +-- /data
```

or:

```text
EC2
 |
 +-- EBS gp3
       |
       +-- ext4
             |
             +-- /data
```

---

# 15. Common EBS Volume + File System Combination

For a normal production application:

```text
EC2
 |
 +-- Root EBS
 |     |
 |     +-- ext4 / XFS
 |
 +-- Data EBS
       |
       +-- gp3
             |
             +-- XFS / ext4
```

A common choice is:

```text
gp3 + XFS
```

or:

```text
gp3 + ext4
```

The choice depends on the workload.

---

# 16. What is EFS?

**Amazon EFS** is different from EBS.

EBS:

```text
EC2
 |
 +-- EBS
       |
       +-- File System
             |
             +-- /data
```

EBS is normally attached to a specific EC2 instance at a time for typical use cases.

EFS:

```text
             EFS
           /  |  \
          /   |   \
       EC2  EC2  EC2
```

Multiple EC2 instances can access the same EFS file system.

EFS is useful when you need **shared file storage** across multiple EC2 instances.

---

# 17. EBS vs EFS

| Feature        | EBS                       | EFS                                 |
| -------------- | ------------------------- | ----------------------------------- |
| Type           | Block storage             | File storage                        |
| Typical access | Single EC2 at a time      | Multiple EC2 instances              |
| Shared storage | Not normally              | Yes                                 |
| File system    | You create/manage it      | AWS manages the file system service |
| Example        | `/data` on EC2            | Shared `/mnt/efs`                   |
| Use case       | Application/database disk | Shared application files            |
| Scaling        | Volume-based              | Elastic                             |

---

# 18. Important Interview Point

### Question:

**What is a file system?**

### Answer:

> A file system is a method used by the operating system to organize, store, and manage files and directories on a storage device. In AWS, when we attach an EBS volume to an EC2 instance, we normally create a file system such as ext4 or XFS and mount it to a directory so that Linux can use the volume for storing data.

---

# 19. Interview Question: Which File System Do You Use?

You can answer:

> For general-purpose Linux workloads, I commonly use ext4 because it is stable and widely supported. For enterprise workloads, large files, or workloads where XFS is preferred, I use XFS. The decision depends on the application requirements and operational needs.

---

# 20. Interview Question: What Happens When You Attach an EBS Volume?

Answer:

> When I attach an EBS volume to an EC2 instance, I first verify the device using `lsblk`. If it is a new volume, I create a file system such as XFS or ext4 using `mkfs`. Then I create a mount point, mount the volume, verify it using `df -h` and `lsblk`, and configure `/etc/fstab` using the UUID if I need the volume to be mounted automatically after reboot.

---

# 21. Important Commands

### List disks

```bash
lsblk
```

### Check mounted file systems

```bash
df -h
```

### Check file system type

```bash
df -Th
```

### Check UUID

```bash
sudo blkid
```

### Create ext4

```bash
sudo mkfs.ext4 /dev/xvdf
```

### Create XFS

```bash
sudo mkfs.xfs /dev/xvdf
```

### Create mount point

```bash
sudo mkdir /data
```

### Mount

```bash
sudo mount /dev/xvdf /data
```

### Unmount

```bash
sudo umount /data
```

### Check mounts

```bash
mount
```

---

# 22. DevOps Practical Flow

A typical AWS production setup can look like:

```text
                    AWS
                     |
                    EC2
                     |
              +------+------+
              |             |
           Root EBS       Data EBS
              |             |
            ext4/XFS      gp3 + XFS
              |             |
              /           /data
                            |
                     Application Data
                            |
               +------------+------------+
               |            |            |
             logs        uploads      application
```

---

# 23. Easy Way to Remember

Remember these three layers:

```text
EBS Volume
    ↓
File System
    ↓
Mount Point
```

Example:

```text
100 GB gp3 EBS
      ↓
     XFS
      ↓
    /data
```

Meaning:

* **EBS** = Provides the storage
* **XFS/ext4** = Organizes the storage
* **/data** = Where Linux exposes the storage

---

# 24. Final Decision Table

| Requirement                 | Recommended Choice                                                                         |
| --------------------------- | ------------------------------------------------------------------------------------------ |
| Normal Linux application    | ext4                                                                                       |
| General-purpose EBS         | ext4 or XFS                                                                                |
| Enterprise workload         | XFS                                                                                        |
| Large files                 | XFS                                                                                        |
| Database                    | Depends on database/workload; XFS/ext4 are common                                          |
| Shared storage across EC2   | EFS                                                                                        |
| High-performance EBS        | Choose EBS type based on IOPS/throughput requirements, then use an appropriate file system |
| Temporary RAM-based storage | tmpfs                                                                                      |

## Key Point

**EBS volume and file system are not the same thing.**

```text
EBS = Storage
File System = Organization of storage
Mount Point = Location where Linux accesses it
```

Example:

```text
AWS EBS gp3
     ↓
    XFS
     ↓
   /data
```

This is the basic concept you should understand before working with Linux volumes, EC2 storage, EBS, and EFS.
