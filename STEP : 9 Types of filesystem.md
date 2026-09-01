# FILE SYSTEM

## 1. What is a File System?

A file system is a method used by an operating system to organize, store, manage, and access files and directories on a storage device.

Example:

Storage Device  
↓  
File System  
↓  
Directories  
↓  
Files

---

# 2. Types of File Systems

## Linux File Systems

Common Linux filesystems:

| File System | Description |
|---|---|
| ext2 | Older Linux filesystem without journaling |
| ext3 | ext2 with journaling |
| ext4 | Common Linux filesystem |
| XFS | High-performance Linux filesystem |
| Btrfs | Modern filesystem with advanced features |
| ZFS | Advanced filesystem and storage management |

For DevOps, focus mainly on:

- ext4
- XFS

---

# 3. ext2

`ext2` stands for Extended File System 2.

### Features

- Older Linux filesystem
- No journaling
- Lightweight
- Less commonly used today

### Create ext2 filesystem

```bash
sudo mkfs.ext2 /dev/nvme1n1
