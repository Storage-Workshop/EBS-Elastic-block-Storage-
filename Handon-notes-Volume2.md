PART 6 — Volume 2: Manual EBS Snapshot

## Objective

In this section, we will:

- Create an additional EBS volume
- Attach it to an EC2 instance
- Format and mount the volume
- Create test data
- Create a manual EBS snapshot
- Verify the snapshot
- Restore the snapshot to a new EBS volume

---

## Step 1: Create EBS Volume

Go to:

```text
AWS Console
   ↓
EC2
   ↓
Volumes
   ↓
Create volume

Configure:

Volume Type → gp3
Size        → 10 GiB
Availability Zone → Same AZ as EC2

Example:

EC2 → ap-south-1a
EBS → ap-south-1a

Click:

Create volume
Step 2: Attach EBS Volume to EC2

Select the newly created volume:

EC2
   ↓
Volumes
   ↓
Select Volume
   ↓
Actions
   ↓
Attach volume

Select the EC2 instance.

Example device name:

/dev/xvdg

Click:

Attach volume

On Nitro-based EC2 instances, /dev/xvdg may appear inside Linux as /dev/nvme3n1, /dev/nvme4n1, etc.

Step 3: Verify the Volume

Connect to the EC2 instance:

lsblk

Example:

NAME        SIZE TYPE MOUNTPOINT
nvme0n1      20G disk
└─nvme0n1p1  20G part /
nvme3n1      10G disk

Here:

/dev/nvme3n1 → Additional EBS Volume
Step 4: Check Filesystem

Before formatting:

sudo file -s /dev/nvme3n1

For a new EBS volume, you may see:

/dev/nvme3n1: data

This indicates there is no recognized filesystem.

⚠️ If the volume contains existing data, DO NOT format it.

Step 5: Format the EBS Volume

Create an ext4 filesystem:

sudo mkfs -t ext4 /dev/nvme3n1

Verify:

lsblk -f

Expected:

nvme3n1  ext4
Step 6: Create Mount Point
sudo mkdir /manual-backup
Step 7: Mount the Volume
sudo mount /dev/nvme3n1 /manual-backup

Verify:

df -h

Expected:

/dev/nvme3n1   10G   ...   /manual-backup
Step 8: Create Test Data

Create a test file:

sudo sh -c 'echo "Manual Snapshot Test Data" > /manual-backup/test.txt'

Check the file:

cat /manual-backup/test.txt

Output:

Manual Snapshot Test Data
Step 9: Create Manual EBS Snapshot

Go to:

AWS Console
   ↓
EC2
   ↓
Volumes

Select the EBS volume used for this section.

Then:

Actions
   ↓
Create snapshot

Enter a description:

Manual EBS Backup - Volume 2

Click:

Create snapshot
Step 10: Verify Snapshot

Go to:

EC2
   ↓
Snapshots

Find the snapshot.

Check:

State → Completed

The snapshot is now available as a backup/recovery point.

Step 11: Restore From Snapshot

To restore the backup:

EC2
   ↓
Snapshots
   ↓
Select Snapshot
   ↓
Actions
   ↓
Create volume from snapshot

Select the required:

Availability Zone → Same AZ as EC2
Volume Type       → gp3

Click:

Create volume
Step 12: Attach Restored Volume

Go to:

EC2
   ↓
Volumes
   ↓
Select Restored Volume
   ↓
Actions
   ↓
Attach volume

Select the EC2 instance.

Example:

Device → /dev/xvdh
Step 13: Verify Restored Data

Connect to EC2:

lsblk

Identify the restored volume.

Check filesystem:

lsblk -f

Create a mount point:

sudo mkdir /restored-data

Mount the restored volume:

sudo mount /dev/nvme4n1 /restored-data

Replace /dev/nvme4n1 with the actual device shown by lsblk.

Check the restored file:

cat /restored-data/test.txt

Expected:

Manual Snapshot Test Data

This confirms that the snapshot successfully restored the data.

Complete Flow
Create EBS Volume
        ↓
Attach to EC2
        ↓
lsblk
        ↓
Check Filesystem
        ↓
Format ext4
        ↓
Create Mount Point
        ↓
Mount
        ↓
Create Test Data
        ↓
Create Manual Snapshot
        ↓
Snapshot Completed
        ↓
Create Volume From Snapshot
        ↓
Attach Restored Volume
        ↓
Mount Restored Volume
        ↓
Verify Data
Important Commands
lsblk

sudo file -s /dev/nvme3n1

sudo mkfs -t ext4 /dev/nvme3n1

sudo mkdir /manual-backup

sudo mount /dev/nvme3n1 /manual-backup

df -h

sudo sh -c 'echo "Manual Snapshot Test Data" > /manual-backup/test.txt'

cat /manual-backup/test.txt

lsblk -f
Important Notes
EBS volumes are Availability Zone specific.
The EBS volume must be in the same AZ as the EC2 instance for direct attachment.
A snapshot is a point-in-time backup of an EBS volume.
Creating a snapshot does not delete or modify the original EBS volume.
You can create a new EBS volume from a snapshot.
The restored volume can be attached to an EC2 instance.
Always verify the device name with lsblk before running filesystem commands.
Never run mkfs on an existing volume containing important data.
Interview Answer

"For a manual EBS backup, I first identify the EBS volume attached to the EC2 instance and create an EBS snapshot from the AWS Console. I wait until the snapshot reaches the Completed state. If I need to restore it, I create a new EBS volume from the snapshot, select the required Availability Zone, attach the volume to an EC2 instance, mount it, and verify that the original data is restored."
