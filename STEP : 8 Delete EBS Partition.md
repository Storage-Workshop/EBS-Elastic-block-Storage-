## Delete EBS Partition

> ⚠️ **Warning:** Deleting a partition can make the data on that partition inaccessible. Make sure you have a backup before doing this.

### Step 1: Unmount the Partition

If `/dev/xvdf1` is mounted on `/data`:

```bash
sudo umount /data
```

Verify:

```bash
lsblk
```

### Step 2: Open fdisk

```bash
sudo fdisk /dev/xvdf
```

Inside `fdisk`:

```text
p → Print partition table
d → Delete partition
1 → Select partition 1
w → Write changes and exit
```

### Step 3: Verify

```bash
lsblk
```

Before:

```text
xvdf
└─xvdf1
```

After:

```text
xvdf
```

The partition `/dev/xvdf1` is now deleted.

### Complete Commands

```bash
sudo umount /data

lsblk

sudo fdisk /dev/xvdf
```

Inside `fdisk`:

```text
p
d
1
w
```

Then:

```bash
lsblk
```

### Important Difference

```text
Delete Partition
        ↓
Removes partition entry
        ↓
Does NOT delete the EBS volume itself
```

If you want to completely remove the EBS storage:

```text
EC2
 ↓
Volumes
 ↓
Select EBS
 ↓
Actions
 ↓
Delete volume
```

**Remember:**

> `fdisk → d → partition number → w` deletes the partition.
> AWS Console **Delete volume** deletes the entire EBS volume.
