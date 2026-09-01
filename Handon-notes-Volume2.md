# Volume 2: Manual EBS Snapshot

In this part, we will create an additional EBS volume, attach it to EC2, mount it, create test data, and take a **manual EBS snapshot**.

## Architecture

```text
EC2 Instance
     |
     └── EBS Volume 2
             |
             ├── Filesystem → ext4
             |
             ├── Mount Point → /manual-backup
             |
             └── Manual Snapshot
