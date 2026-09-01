## Automatic EBS Backup

### Using Amazon Data Lifecycle Manager (DLM)

DLM can automatically create and manage **EBS snapshots** on a schedule.

### Flow

```text
EBS Volume
    ↓
DLM Policy
    ↓
Automatic Snapshot
    ↓
Retention Period
    ↓
Old Snapshots Deleted Automatically
```

### Example

```text
EBS Volume
    ↓
Every Day → Create Snapshot
    ↓
Keep Last 7 Snapshots
    ↓
Delete Older Snapshots
```

### Steps

```text
EC2
 ↓
Lifecycle Manager
 ↓
Create Lifecycle Policy
 ↓
Select EBS Snapshot Policy
 ↓
Select Target Volumes using Tags
 ↓
Set Schedule
 ↓
Set Retention
 ↓
Create Policy
```

### Example Configuration

```text
Schedule       → Daily
Time           → 02:00 AM
Retention      → 7 snapshots
Target         → EBS volumes with tag:
                 Backup = Daily
```

### Tag the EBS Volume

```text
Key   → Backup
Value → Daily
```

DLM identifies the EBS volume using this tag and automatically creates snapshots according to the policy.

### Important Points

* DLM automates **EBS snapshot creation**.
* We can define a backup **schedule**.
* We can define **retention**.
* Old snapshots can be automatically deleted.
* Tags can be used to identify which EBS volumes should be backed up.
* This reduces the need to create snapshots manually.

### Interview Answer

> **"For automatic EBS backups, we can use Amazon Data Lifecycle Manager. We create a lifecycle policy, select the EBS volumes using tags, define a snapshot schedule and retention period. DLM then automatically creates and manages EBS snapshots according to the policy."**


## AWS Backup

### What is AWS Backup?

* **AWS Backup** is a fully managed service used to automate and centrally manage backups.
* It can back up services such as **EBS, EC2, RDS, EFS, DynamoDB**, and others.
* We can define backup schedules and retention policies.
* It provides centralized monitoring of backup jobs.
* We can use backup policies across multiple AWS resources.

### EBS Backup Using AWS Backup

```text id="bkpflow"
EBS Volume
     ↓
AWS Backup
     ↓
Backup Plan
     ↓
Backup Rule
     ↓
Automatic Backup
     ↓
Recovery Point
```

### Steps to Configure

```text id="q4k8p1"
AWS Console
    ↓
AWS Backup
    ↓
Create Backup Plan
    ↓
Create New Plan
    ↓
Backup Rule
    ↓
Set Backup Frequency
    ↓
Set Backup Window
    ↓
Set Retention Period
    ↓
Assign Resources
    ↓
Select EBS / EC2
    ↓
Create Backup Plan
```

### Example

```text id="m2b4u7"
Backup Frequency → Daily
Backup Time      → 02:00 AM
Retention        → 7 Days
Resource         → EBS Volume
```

AWS Backup will automatically create recovery points according to the backup plan.

### Backup Flow

```text id="j3p9qa"
EBS Volume
    ↓
AWS Backup Plan
    ↓
Daily Backup
    ↓
Recovery Point
    ↓
Retain for 7 Days
    ↓
Old Recovery Point Expires
```

### Restore

If the EBS volume is lost or the data needs to be recovered:

```text id="restor1"
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
Mount Volume
```

### Important Terms

* **Backup Plan** → Defines when and how backups are taken.
* **Backup Rule** → Defines schedule, backup window, and lifecycle.
* **Backup Vault** → Stores recovery points.
* **Recovery Point** → A backup created by AWS Backup.
* **Retention Period** → Defines how long backups are kept.
* **Restore** → Creates/restores the resource from a recovery point.

### AWS Backup vs DLM

| AWS Backup                     | DLM                                  |
| ------------------------------ | ------------------------------------ |
| Centralized backup service     | EBS lifecycle management             |
| Supports multiple AWS services | Mainly EBS snapshots                 |
| Backup plans and vaults        | Lifecycle policies                   |
| Centralized monitoring         | Simpler EBS automation               |
| Suitable for enterprise backup | Suitable for EBS snapshot automation |

### Interview Answer

> **"For automated EBS backups, we can use AWS Backup. We create a backup plan, define the backup schedule and retention period, assign the EBS volumes as resources, and AWS Backup automatically creates recovery points. If required, we can restore the backup to a new EBS volume and attach it to an EC2 instance."**
