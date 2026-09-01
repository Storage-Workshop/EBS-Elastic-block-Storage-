# Create Automatic EBS Backup Using AWS Backup

## Step 1: Open AWS Backup

```text
AWS Console
   ↓
Search "AWS Backup"
   ↓
Open AWS Backup
```

## Step 2: Create Backup Plan

Go to:

```text
AWS Backup
   ↓
Backup plans
   ↓
Create backup plan
```

Choose:

```text
Create a new plan
```

Give the plan a name:

```text
EBS-Daily-Backup
```

## Step 3: Create Backup Rule

Configure the rule:

```text
Backup rule name → Daily-EBS-Backup
Backup vault     → Default
Backup frequency → Daily
```

For example:

```text
Daily
↓
Every day at 02:00 AM
```

Set the retention period:

```text
Retention → 7 days
```

Then click:

```text
Create plan
```

## Step 4: Assign EBS Resources

After creating the plan:

```text
Backup Plan
   ↓
Assign resources
```

Choose:

```text
Resource assignment
```

You can select resources using **tags**.

Example:

```text
Key   → Backup
Value → Daily
```

Only EBS volumes with this tag will be selected.

## Step 5: Add Tag to EBS Volume

Go to:

```text
EC2
 ↓
Volumes
 ↓
Select EBS Volume
 ↓
Tags
```

Add:

```text
Key   = Backup
Value = Daily
```

## Step 6: Automatic Backup

Now AWS Backup automatically follows the backup plan:

```text
EBS Volume
    ↓
Tag: Backup=Daily
    ↓
AWS Backup
    ↓
Daily Backup
    ↓
Recovery Point
    ↓
Keep for 7 Days
```

## Step 7: Verify Backup

Go to:

```text
AWS Backup
   ↓
Backup vaults
   ↓
Default
   ↓
Recovery points
```

You should see the EBS recovery point.

## Step 8: Restore

If you need to restore the backup:

```text
Backup Vault
     ↓
Recovery Point
     ↓
Select Recovery Point
     ↓
Restore
     ↓
Configure Restore
     ↓
Restore Backup
```

AWS Backup can restore the EBS backup to a new volume.

# Important Terms

| Term                | Meaning                               |
| ------------------- | ------------------------------------- |
| Backup Plan         | Defines how backups are performed     |
| Backup Rule         | Defines schedule and retention        |
| Backup Vault        | Stores recovery points                |
| Recovery Point      | Backup created by AWS Backup          |
| Retention           | How long backup is kept               |
| Resource Assignment | Defines which resources are backed up |

# Interview Answer

> **"For automatic EBS backups, I use AWS Backup. I create a backup plan, configure a daily backup rule and retention period, and assign the required EBS volumes using tags. AWS Backup then automatically creates recovery points according to the schedule. If required, I can restore a recovery point to create a new EBS volume."**
