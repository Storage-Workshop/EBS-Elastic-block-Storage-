# ROOT VOLUME

# Device Name

<img width="600" height="83" alt="image" src="https://github.com/user-attachments/assets/167f5eda-58aa-45a5-b918-62e2e14cc805" />

# Block Device

### DelateOnTermination -> yes

<img width="712" height="128" alt="image" src="https://github.com/user-attachments/assets/64d401a2-fc84-43c2-ac07-0bf4b1d012cd" />

## List The Block Storage

```markdown

lsblk

```

<img width="365" height="110" alt="image" src="https://github.com/user-attachments/assets/30efea4c-f11b-4abf-b99e-a2346619c0a4" />

```markdown
dh -h
```

<img width="445" height="143" alt="image" src="https://github.com/user-attachments/assets/04ec91b8-c47b-4e18-add7-2eb51767c03c" />

# Addiational VOLUME

# Device Name

<img width="623" height="83" alt="image" src="https://github.com/user-attachments/assets/e9131d1d-c65e-47ae-98a8-0e3ebb0c4cd6" />

# Block Device

### DelateOnTermination -> No

<img width="743" height="137" alt="image" src="https://github.com/user-attachments/assets/6aff3098-5c9d-40de-a075-c4f9b3b501df" />

## List The Block Storage

```markdown
lsblk
```

<img width="351" height="97" alt="image" src="https://github.com/user-attachments/assets/121bfeed-42d8-4d4f-9bdc-83c639f666ac" />

## what type of data/filesystem is present on the EBS volume.

```markdown
file -s /dev/nvme1n1
```

- file → identifies the type of a file/device.
- -s → tells file to inspect the contents of the device, rather than treating it like a normal file.
- /dev/xvdf → the EBS block device attached to your EC2 instance.

<img width="326" height="45" alt="image" src="https://github.com/user-attachments/assets/c725095d-a522-4afc-ae98-cef537e90b5d" />

## Formatting the EBS Volume

For an unused volume:

`sudo mkfs -t ext4 /dev/xvdb`

<img width="921" height="189" alt="image" src="https://github.com/user-attachments/assets/05cdc719-6376-4f8e-b744-c53ed42bbb72" />

#### Be careful: 
`mkfs formats the disk and can destroy existing data.`

# create a Directory 

```markdown
mkdir /myapplication
```

# Creating a Mount Point
```markdown
sudo mkdir /myapplication
```

## Mount the volume:
```markdown
sudo mount /dev/nvme1n1 /myapplication
```
### Verify:
```markdown
df -h
```
<img width="457" height="145" alt="image" src="https://github.com/user-attachments/assets/c2708ac7-9144-4f89-a2f4-f7fd5d05a946" />

Now /dev/nvme1n1 is mounted on /myapplication.

# Making the Mount Persistent

## Get the UUID:

```markdown
sudo blkid /dev/nvme1n1
```
### Example:

```markdown
/dev/nvme1n1: UUID="199737d5-7a95-4ae1-916b-36d37379311a" BLOCK_SIZE="4096" TYPE="ext4"
```

## Add it to /etc/fstab:

```
UUID="199737d5-7a95-4ae1-916b-36d37379311a" /myapplication ext4 defaults,nofail 0 2
```

Then test:

sudo mount -a

If there is no error, the configuration is valid.
