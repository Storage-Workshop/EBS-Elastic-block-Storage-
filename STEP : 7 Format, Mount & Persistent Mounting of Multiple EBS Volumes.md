## Format, Mount & Persistent Mounting of Multiple EBS Volumes

Suppose we have:

```text
EC2
 |
 |--- Root EBS → /dev/xvda → /
 |
 |--- Data EBS → /dev/xvdf → /data
 |
 |--- Logs EBS → /dev/xvdg → /logs
```

### Step 1: Check Volumes

```bash
lsblk
```

```bash
sudo file -s /dev/xvdf
sudo file -s /dev/xvdg
```

> If these are new volumes, we can format them. If they contain existing data, **do not format them**.

### Step 2: Format Volumes

```bash
sudo mkfs -t ext4 /dev/xvdf
sudo mkfs -t ext4 /dev/xvdg
```

### Step 3: Create Mount Points

```bash
sudo mkdir /data
sudo mkdir /logs
```

### Step 4: Mount Volumes

```bash
sudo mount /dev/xvdf /data
sudo mount /dev/xvdg /logs
```

Verify:

```bash
df -h
```

### Step 5: Get UUIDs

```bash
sudo blkid /dev/xvdf
sudo blkid /dev/xvdg
```

Example:

```text
/dev/xvdf: UUID="1111-aaaa" TYPE="ext4"
/dev/xvdg: UUID="2222-bbbb" TYPE="ext4"
```

### Step 6: Configure `/etc/fstab`

```bash
sudo vi /etc/fstab
```

Add:

```text
UUID=1111-aaaa  /data  ext4  defaults,nofail  0  2
UUID=2222-bbbb  /logs  ext4  defaults,nofail  0  2
```

### Step 7: Test

```bash
sudo mount -a
```

If there is no error:

```bash
df -h
lsblk
```

### Step 8: Reboot and Verify

```bash
sudo reboot
```

After reboot:

```bash
df -h
```

The volumes should automatically mount:

```text
/dev/xvdf → /data
/dev/xvdg → /logs
```

### Complete Flow

```text
Attach EBS
    ↓
lsblk
    ↓
Check Filesystem
    ↓
Format (only if new)
    ↓
Create Mount Points
    ↓
Mount
    ↓
Get UUID
    ↓
Add UUID to /etc/fstab
    ↓
mount -a
    ↓
Reboot
    ↓
Automatically Mounted
```

### Commands in One Place

```bash
lsblk

sudo file -s /dev/xvdf
sudo file -s /dev/xvdg

sudo mkfs -t ext4 /dev/xvdf
sudo mkfs -t ext4 /dev/xvdg

sudo mkdir /data
sudo mkdir /logs

sudo mount /dev/xvdf /data
sudo mount /dev/xvdg /logs

sudo blkid

sudo vi /etc/fstab

sudo mount -a

df -h
lsblk
```

### Important Rule

> **New EBS → Format → Mount → UUID → `/etc/fstab` → `mount -a` → Reboot → Verify**

> **Existing EBS with data → Do NOT format. Check filesystem → Mount → Configure `/etc/fstab`.**
