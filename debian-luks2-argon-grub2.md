Download a __standard__ Debian ISO (no GUI, debian-live-testing-amd64-standard.iso) from [here](https://cdimage.debian.org/cdimage/weekly-live-builds/amd64/iso-hybrid/?C=S;O=D).  
Create a Virtualbox VM, enable UEFI, 2GB RAM, a 18GB disk size, with the downloaded ISO as boot.  
Set the networking to __Bridged adapter__ in Virtualbox, so that you get an IP address from the router and can access internet. NAT doesn't seem to work.  
Start the VM, choose the live boot, install ssh server.
```
sudo apt install openssh-server
```
Add a password to the `user` account. You will need it to connect to the VM via ssh from your laptop.  
```
sudo passwd user
```
Connect to the Debian VM via ssh.
```
ssh user@192.168.1.101
```
Create the mount point for the installation.
```
sudo mkdir -vp /mnt/debian
```
For binary packages, replace `trixie` with `sid` and add `non-free` packages to `/etc/apt/sources.list`:
```
sudo su -c "sed 's|deb http://deb.debian.org/debian/ trixie main non-free-firmware|\
deb http://deb.debian.org/debian/ sid main contrib non-free non-free-firmware|g' -i "/etc/apt/sources.list""
```
For source packages, replace `trixie` with `sid` and add `non-free` packages to `/etc/apt/sources.list`:
```
sudo su -c "sed 's|deb-src http://deb.debian.org/debian/ trixie|\
deb-src http://deb.debian.org/debian/ sid|g' -i "/etc/apt/sources.list""
```
Update the system.
```
sudo apt update && sudo apt upgrade
```
You might need to do this if the package manager complains about no free disk space:
```
sudo apt --fix-broken install
```
Install the bootstrap tools and `gdisk` to partition disk.
```
sudo apt install arch-install-scripts debootstrap gdisk cryptsetup dosfstools -y
```
Use `lsblk` to see your disks, partitions. The `sda` disk is 15GB in size, we will use it to install Debian.
```
user@debian:~$ lsblk
NAME  MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
loop0   7:0    0  1.2G  1 loop /usr/lib/live/mount/rootfs/filesystem.squashfs
                               /run/live/rootfs/filesystem.squashfs
sda     8:0    0   15G  0 disk
sr0    11:0    1  1.7G  0 rom  /usr/lib/live/mount/medium
                               /run/live/medium
```
Use `gdisk` to create a 100MB EFI partition and the rest of the disk for the Debian install.
```
user@debian:~$ sudo gdisk /dev/sda
GPT fdisk (gdisk) version 1.0.10

Partition table scan:
  MBR: not present
  BSD: not present
  APM: not present
  GPT: not present

Creating new GPT entries in memory.

Command (? for help): p
Disk /dev/sda: 31457280 sectors, 15.0 GiB
Model: VBOX HARDDISK
Sector size (logical/physical): 512/512 bytes
Disk identifier (GUID): 0EF0216F-A157-4AA8-A427-E8C982F83F28
Partition table holds up to 128 entries
Main partition table begins at sector 2 and ends at sector 33
First usable sector is 34, last usable sector is 31457246
Partitions will be aligned on 2048-sector boundaries
Total free space is 31457213 sectors (15.0 GiB)

Number  Start (sector)    End (sector)  Size       Code  Name

Command (? for help): n
Partition number (1-128, default 1):
First sector (34-31457246, default = 2048) or {+-}size{KMGTP}:
Last sector (2048-31457246, default = 31455231) or {+-}size{KMGTP}: +100MB
Current type is 8300 (Linux filesystem)
Hex code or GUID (L to show codes, Enter = 8300): ef00
Changed type of partition to 'EFI system partition'

Command (? for help): n
Partition number (2-128, default 2):
First sector (34-31457246, default = 206848) or {+-}size{KMGTP}:
Last sector (206848-31457246, default = 31455231) or {+-}size{KMGTP}:
Current type is 8300 (Linux filesystem)
Hex code or GUID (L to show codes, Enter = 8300):
Changed type of partition to 'Linux filesystem'

Command (? for help): p
Disk /dev/sda: 31457280 sectors, 15.0 GiB
Model: VBOX HARDDISK
Sector size (logical/physical): 512/512 bytes
Disk identifier (GUID): 0EF0216F-A157-4AA8-A427-E8C982F83F28
Partition table holds up to 128 entries
Main partition table begins at sector 2 and ends at sector 33
First usable sector is 34, last usable sector is 31457246
Partitions will be aligned on 2048-sector boundaries
Total free space is 4029 sectors (2.0 MiB)

Number  Start (sector)    End (sector)  Size       Code  Name
   1            2048          206847   100.0 MiB   EF00  EFI system partition
   2          206848        31455231   14.9 GiB    8300  Linux filesystem

Command (? for help): w

Final checks complete. About to write GPT data. THIS WILL OVERWRITE EXISTING
PARTITIONS!!

Do you want to proceed? (Y/N): Y
OK; writing new GUID partition table (GPT) to /dev/sda.
The operation has completed successfully.
```
Create the LUKS2 partition. Make sure the partition is correct - `/dev/sda2` in this case.
```
sudo cryptsetup luksFormat --pbkdf=argon2id --use-urandom -s 512 -h sha512 -i 10000 /dev/sda2
```
Enter your passphrase twice, without errors, wait for completion.
```
WARNING!
========
This will overwrite data on /dev/sda2 irrevocably.

Are you sure? (Type 'yes' in capital letters): YES
Enter passphrase for /dev/sda2:
Verify passphrase:
```
Open the LUKS2 partition, make sure you have the correct `/dev/` partition.
```
sudo cryptsetup open /dev/sda2 debian-cryptlvm
```
Create the physical volume.
```
sudo pvcreate /dev/mapper/debian-cryptlvm
```
Create the volume group.
```
sudo vgcreate vg1 /dev/mapper/debian-cryptlvm
```
Create the logical volume for the swap partition.
```
sudo lvcreate -L 2G vg1 -n swap
```
Create the logical volume for the root partition.
```
sudo lvcreate -l +100%FREE vg1 -n root
```
Create the EFI filesystem:
```
sudo mkfs.fat -F 32 /dev/sda1
```
Create the swap file:
```
sudo mkswap -L swap /dev/vg1/swap
```
Create the root ext4 filesystem:
```
sudo mkfs.ext4 -L 'root' /dev/vg1/root
```
Mount root filesystem:
```
sudo mount -v /dev/vg1/root /mnt/debian
```
Mount EFI partition to its folder, check if `/dev/` is correct first:
```
sudo mkdir -vp /mnt/debian/efi; sudo mount -v /dev/sda1 /mnt/debian/efi
```
We will now activate the swap file:
```
sudo swapon /dev/mapper/vg1-swap
```
Let's see what we've accomplished, use `lsblk`:
```
user@debian:~$ lsblk
NAME                MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINTS
loop0                 7:0    0  1.2G  1 loop  /usr/lib/live/mount/rootfs/filesystem.squashfs
                                              /run/live/rootfs/filesystem.squashfs
sda                   8:0    0   15G  0 disk
├─sda1                8:1    0  100M  0 part  /mnt/debian/efi
└─sda2                8:2    0 14.9G  0 part
  └─debian-cryptlvm 253:0    0 14.9G  0 crypt
    ├─vg1-swap      253:1    0    2G  0 lvm   [SWAP]
    └─vg1-root      253:2    0 12.9G  0 lvm   /mnt/debian
sr0                  11:0    1  1.7G  0 rom   /usr/lib/live/mount/medium
                                              /run/live/medium
```
Let's bootstratp the `sid` installation:
```
sudo debootstrap --arch amd64 sid /mnt/debian https://deb.debian.org/debian
```
