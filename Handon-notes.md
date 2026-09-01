# PART 1 — Create 4 Additional EBS Volumes
## Step 1: Go to EC2 Volumes
```
AWS Console
   ↓
EC2
   ↓
Volumes
   ↓
Create volume
```

## Create the first volume:

Volume Type → gp3
Size        → 30 GiB
AZ          → ap-south-1a

## Create the second:

Volume Type → gp3
Size        → 10 GiB
AZ          → ap-south-1a

## Create the third:

Volume Type → gp3
Size        → 5 GiB
AZ          → ap-south-1a

## Create the Four:

Volume Type → gp3
Size        → 5 GiB
AZ          → ap-south-1a

## You now have:

- Volume 1 → 30 GB → 1a
- Volume 2 → 10 GB → 1a
- Volume 3 → 5 GB → 1a
- Volume 4 → 5 GB → 1a

<img width="756" height="242" alt="image" src="https://github.com/user-attachments/assets/66f99f3d-85b8-4824-80bc-d2f1e35a826f" />

# PART 2 — Attach All 3 Volumes

Go to:
```
EC2
 ↓
Volumes
 ↓
Select Volume 1
 ↓
Actions
 ↓
Attach volume

```

### Select your EC2 instance.

```
Device:

/dev/xvdf

Repeat for Volume 2:

/dev/xvdg

Repeat for Volume 3:

/dev/xvdh

Repeat for Volume 4:

/dev/xvdi

```

<img width="749" height="335" alt="image" src="https://github.com/user-attachments/assets/acf7e5d5-4068-48da-9e1c-db6c193b7a6a" />


# PART 3 — Verify All Volumes

### SSH into the EC2:
```

lsblk

```

### Expected:

<img width="345" height="143" alt="image" src="https://github.com/user-attachments/assets/e48c9d07-51bf-4fbe-a599-11a9bfcbb7fe" />
