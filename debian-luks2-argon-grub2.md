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
Let's generate a `fstab`:
```
sudo genfstab -U /mnt/debian >> /mnt/debian/etc/fstab
```
We can set the hostname:
```
sudo sh -c "echo argon > /mnt/debian/etc/hostname"
```
We can add the hostname to the `hosts` file:
```
sudo sh -c "echo '127.0.1.1      argon' >> /mnt/debian/etc/hosts"
```
Let's chroot into the new installation as `root`.
```
sudo arch-chroot /mnt/debian /bin/bash --login
```
Set the new password for the `root` user:
```
passwd
```
Let's update the system.
```
apt update && apt upgrade
```
We will install locales.
```
apt install locales -y
```
We will configure the system locale. Choose `en_GB.UTF-8` or whatever is suitable.
```
dpkg-reconfigure locales
```
We will configure the time zone.
```
dpkg-reconfigure tzdata
```
These packages are needed to generate the kernel image for the encrypted root partition.
```
apt install initramfs-tools cryptsetup cryptsetup-initramfs -y
```
We can use the swap file for hibernation (optional):
```
> /etc/initramfs-tools/conf.d/resume printf 'RESUME=%s\n' "$(grep '[[:blank:]]swap' /etc/fstab | grep UU | cut -c '-41')"
```
Check if this worked:
```
cat /etc/initramfs-tools/conf.d/resume
```
Let's install tools for lvm:
```
apt install --fix-missing lvm2 linux-headers-amd64 linux-image-amd64
```
Let's install tools to compile grub:
```
apt install --fix-missing shim-signed shim-helpers-amd64-signed libalpm13t64 \
  sudo git curl libarchive-tools help2man python3 rsync texinfo texinfo-lib \
  ttf-bitstream-vera build-essential dosfstools efibootmgr uuid-runtime efivar \
  mtools os-prober dmeventd libdevmapper-dev libdevmapper-event1.02.1 libdevmapper1.02.1 \
  libfont-freetype-perl python3-freetype libghc-gi-freetype2-dev libghc-gi-freetype2-prof \
  fuse2fs libconfuse2 libfuse2t64 gettext xorriso libisoburn1t64 libisoburn-dev autogen \
  gnulib libfreetype-dev pkg-config m4 libtool automake flex fuse3 libfuse3-dev gawk \
  autoconf-archive rdfind fonts-dejavu lzma lzma-dev liblzma5 liblzma-dev liblz1 liblz-dev \
  unifont acl libzfslinux-dev sbsigntool
```
Create keys folder.
```
mkdir -vp /etc/keys
```
Generate key.
```
( umask 0077 && dd if=/dev/urandom bs=1 count=128 of=/etc/keys/root.key conv=excl,fsync )
```
Change owner to `root`.
```
chown -vR root:root /etc/keys
```
Set perms.
```
chmod -vR 600 /etc/keys
```
Configure `crypttab`.
```
echo "debian-cryptlvm UUID=$(blkid -s UUID -o value /dev/sda2) \
  /etc/keys/root.key luks,discard,key-slot=1" >> /etc/crypttab
```
Add the key to `initramfs`.
```
echo "KEYFILE_PATTERN=\"/etc/keys/*.key\"" >>/etc/cryptsetup-initramfs/conf-hook
```
Yes.
```
echo UMASK=0077 >>/etc/initramfs-tools/initramfs.conf
```
```
apt-mark hold grub2 grub-pc grub-efi grub-efi-amd64
useradd -mG cdrom,floppy,sudo,audio,dip,video,plugdev,netdev -s /usr/bin/bash -c 'George' george
passwd george
export PATH="$PATH:/bin/gcc:/sbin/gcc"; export GRUB_CONTRIB=./grub-extras; \
  export GNULIB_SRCDIR=./gnulib; export CFLAGS=${CFLAGS/-fno-plt}

mv /usr/bin/mawk /usr/bin/mawk_bu;ln -s /usr/bin/gawk /usr/bin/mawk
mkdir -vp /sources && cd sources
git clone https://git.savannah.gnu.org/git/grub.git
cd grub
git clone https://git.savannah.nongnu.org/git/grub-extras.git;\
  git clone https://aur.archlinux.org/grub-improved-luks2-git.git; \
  git clone https://git.savannah.gnu.org/git/gnulib.git
patch -Np1 -i ./grub-improved-luks2-git/add-GRUB_COLOR_variables.patch; \
  patch -Np1 -i ./grub-improved-luks2-git/detect-archlinux-initramfs.patch

patch -Np1 -i ./grub-improved-luks2-git/argon_1.patch; patch -Np1 \
  -i ./grub-improved-luks2-git/argon_2.patch; patch -Np1 \
  -i ./grub-improved-luks2-git/argon_3.patch; patch -Np1 \
  -i ./grub-improved-luks2-git/argon_4.patch; patch -Np1 \
  -i ./grub-improved-luks2-git/argon_5.patch

patch -Np1 -i ./grub-improved-luks2-git/grub-install_luks2.patch

sed 's|/usr/share/fonts/dejavu|/usr/share/fonts/dejavu \
  /usr/share/fonts/truetype/dejavu|g' -i "configure.ac"
sed 's|GNU/Linux|Linux|' -i "util/grub.d/10_linux.in"
sed 's| ro | rw |g' -i "util/grub.d/10_linux.in"
rm -rf ./grub-extras/lua

./bootstrap
mkdir ./build_x86_64-efi; cd ./build_x86_64-efi

../configure --with-platform=efi --target=x86_64 --prefix="/usr" --sbindir="/usr/bin" \
  --sysconfdir="/etc" --enable-boot-time --enable-cache-stats --enable-device-mapper \
  --enable-grub-mkfont --enable-grub-mount --enable-mm-debug --disable-silent-rules \
  --disable-werror  CPPFLAGS="$CPPFLAGS -O2" --enable-stack-protector --enable-liblzma

make -j3 DESTDIR=/ bashcompletiondir=/usr/share/bash-completion/completions install
install -D -m0644 ../grub-improved-luks2-git/grub.default /etc/default/grub; cd ../../..

sed -i 's|GRUB_DISTRIBUTOR="Arch"|GRUB_DISTRIBUTOR="Debian"|g' /etc/default/grub; \
  sed -i 's|#GRUB_ENABLE_CRYPTODISK=y|GRUB_ENABLE_CRYPTODISK=y|g' /etc/default/grub
mkdir /boot/grub; grub-mkconfig -o /boot/grub/grub.cfg

grub-install --target=x86_64-efi --efi-directory=/efi --boot-directory=/boot \
  --modules="bli argon2 all_video boot btrfs cat chain configfile echo efifwsetup efinet \
  ext2 fat font gettext gfxmenu gfxterm gfxterm_background gzio halt help hfsplus iso9660 \
  jpeg keystatus loadenv loopback linux ls lsefi lsefimmap lsefisystab lssal memdisk minicmd \
  normal ntfs part_apple part_msdos part_gpt password_pbkdf2 png probe reboot regexp search \
  search_fs_uuid search_fs_file search_label serial sleep smbios squash4 test tpm true video \
  xfs cpuid play cryptodisk gcry_arcfour gcry_blowfish gcry_camellia gcry_cast5 gcry_crc \
  gcry_des gcry_dsa gcry_idea gcry_md4 gcry_md5 gcry_rfc2268 gcry_rijndael gcry_rmd160 \
  gcry_rsa gcry_seed gcry_serpent gcry_sha1 gcry_sha256 gcry_sha512 gcry_tiger gcry_twofish \
  gcry_whirlpool luks luks2 lvm mdraid09 mdraid1x raid5rec raid6rec" /dev/sda

mkdir -vp /efi/EFI/BOOT; cp /efi/EFI/debian/grubx64.efi /efi/EFI/BOOT/; \
  cp /usr/lib/shim/shimx64.efi /efi/EFI/BOOT/bootx64.efi; \
  cp /usr/lib/shim/mmx64.efi /efi/EFI/BOOT/mmx64.efi

efibootmgr -c -d /dev/sdb -p 1 -L  "Debian" -l '\EFI\BOOT\bootx64.efi'

logout
sudo swapoff /dev/mapper/vg1-swap
sudo umount -vr /mnt/debian/efi
sudo umount -vr /mnt/debian
sudo cryptsetup close /dev/vg1/swap; sudo cryptsetup close /dev/vg1/root; \
  cryptsetup close debian-cryptlvm

```
