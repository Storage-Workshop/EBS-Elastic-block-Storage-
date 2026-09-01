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

Interview Answer

"For a manual EBS backup, I first identify the EBS volume attached to the EC2 instance and create an EBS snapshot from the AWS Console. I wait until the snapshot reaches the Completed state. If I need to restore it, I create a new EBS volume from the snapshot, select the required Availability Zone, attach the volume to an EC2 instance, mount it, and verify that the original data is restored."
