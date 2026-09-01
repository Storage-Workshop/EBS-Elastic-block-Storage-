# Amazon Data Lifecycle Manager (DLM)

## What is DLM?

* **DLM** stands for **Data Lifecycle Manager**.
* DLM is used to **automatically create and manage EBS snapshots**.
* It helps automate **backup and retention** of EBS volumes.
* We define a **lifecycle policy**.
* DLM uses **tags** to identify which EBS volumes should be backed up.

## DLM Flow

```text
EBS Volume
     ↓
Tag: Backup = Daily
     ↓
DLM Lifecycle Policy
     ↓
Create Snapshot Automatically
     ↓
Retain Snapshot
     ↓
Delete Old Snapshot
```

## Prerequisites

* EBS volume
* EC2 instance is optional for the snapshot itself
* EBS volume must have the required **tag**
* IAM permissions for DLM

## Step 1: Add Tag to EBS Volume

Go to:

```text
EC2
 ↓
Volumes
 ↓
Select EBS Volume
 ↓
Tags
 ↓
Manage Tags
```

Add:

```text
Key   → Backup
Value → Daily
```

Example:

```text
Backup = Daily
```

## Step 2: Create Lifecycle Policy

Go to:

```text
EC2
 ↓
Lifecycle Manager
 ↓
Create Lifecycle Policy
```

Select:

```text
Policy Type → EBS Snapshot Policy
```

## Step 3: Select Target Resources

Choose:

```text
Target Resources
 ↓
Resource Type → Volume
 ↓
Tag
```

Enter:

```text
Key   → Backup
Value → Daily
```

DLM will identify EBS volumes with this tag.

## Step 4: Configure Schedule

Example:

```text
Schedule → Daily
Time     → 02:00 AM
```

You can configure the frequency according to your requirement.

## Step 5: Configure Retention

Example:

```text
Retention → 7 Snapshots
```

Meaning:

```text
Day 1 → Snapshot 1
Day 2 → Snapshot 2
Day 3 → Snapshot 3
...
Day 8 → Old snapshot removed
```

DLM automatically manages the lifecycle according to the policy.

## Complete Example

```text
EBS Volume
   |
   | Tag: Backup=Daily
   ↓
DLM Policy
   |
   | Daily at 02:00 AM
   ↓
EBS Snapshot
   |
   | Keep 7 snapshots
   ↓
Old Snapshot
   |
   ↓
Automatically Deleted
```

## DLM vs AWS Backup

| DLM                             | AWS Backup                     |
| ------------------------------- | ------------------------------ |
| Mainly used for EBS snapshots   | Centralized backup service     |
| Simple EBS lifecycle automation | Supports multiple AWS services |
| Uses lifecycle policies         | Uses backup plans              |
| Uses tags to select volumes     | Resource assignment            |
| Manages snapshot retention      | Backup vault + recovery points |

## Important Points

* DLM **does not create an EBS volume**.
* DLM automatically creates **EBS snapshots**.
* Tags are commonly used to identify target EBS volumes.
* We can configure **backup frequency**.
* We can configure **retention**.
* Old snapshots can be automatically deleted according to the lifecycle policy.
* DLM is useful when we want simple, automated EBS snapshot management.

## Interview Answer

> **"Amazon Data Lifecycle Manager, or DLM, is used to automate the creation, retention, and deletion of EBS snapshots. I create a lifecycle policy, identify the target EBS volumes using tags, configure the snapshot schedule and retention period, and DLM automatically manages the snapshots according to that policy."**
