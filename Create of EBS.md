# ROOT VOLUME

# Device Name

<img width="600" height="83" alt="image" src="https://github.com/user-attachments/assets/167f5eda-58aa-45a5-b918-62e2e14cc805" />

# Block Device

### DelateOnTermination -> yes

<img width="712" height="128" alt="image" src="https://github.com/user-attachments/assets/64d401a2-fc84-43c2-ac07-0bf4b1d012cd" />

## List The Block Storage

`lsblk`

<img width="365" height="110" alt="image" src="https://github.com/user-attachments/assets/30efea4c-f11b-4abf-b99e-a2346619c0a4" />

`dh -h`

<img width="445" height="143" alt="image" src="https://github.com/user-attachments/assets/04ec91b8-c47b-4e18-add7-2eb51767c03c" />

# Addiational VOLUME

# Device Name

<img width="623" height="83" alt="image" src="https://github.com/user-attachments/assets/e9131d1d-c65e-47ae-98a8-0e3ebb0c4cd6" />

# Block Device

### DelateOnTermination -> No

<img width="743" height="137" alt="image" src="https://github.com/user-attachments/assets/6aff3098-5c9d-40de-a075-c4f9b3b501df" />

## List The Block Storage

`lsblk`

## what type of data/filesystem is present on the EBS volume.

`file -s /dev/xvdf`

- file → identifies the type of a file/device.
- -s → tells file to inspect the contents of the device, rather than treating it like a normal file.
- /dev/xvdf → the EBS block device attached to your EC2 instance.
