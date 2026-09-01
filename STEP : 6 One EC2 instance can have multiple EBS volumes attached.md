## Multiple EBS Volumes on One EC2

* We can attach **multiple EBS volumes** to a single EC2 instance.
* All EBS volumes must be in the **same Availability Zone** as the EC2 instance.
* Each volume can be used for a different purpose.
* Each volume can have its own filesystem and mount point.

### Example

```text
EC2 Instance
ap-south-1a
     |
     |--- Root EBS → /dev/xvda → /
     |
     |--- Data EBS → /dev/xvdf → /data
     |
     |--- Logs EBS → /dev/xvdg → /logs
     |
     |--- Backup EBS → /dev/xvdh → /backup
```

### Check Attached Volumes

```bash
lsblk
```

Example:

```text
NAME    SIZE TYPE MOUNTPOINT
xvda     20G disk
└─xvda1  20G part /
xvdf     50G disk
└─xvdf1  50G part /data
xvdg     30G disk
└─xvdg1  30G part /logs
```

### Important Point

> **One EC2 instance can have multiple EBS volumes attached. The exact maximum depends on the EC2 instance type and its EBS attachment limits.**

### Interview Answer

> **"Yes, we can attach multiple EBS volumes to a single EC2 instance. For example, we can use one volume for the operating system, another for application data, and another for logs. The EBS volumes must be in the same Availability Zone as the EC2 instance, and the maximum number of attachments depends on the instance type."**
