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
Install the bootstrap tools.
```
sudo apt install arch-install-scripts debootstrap -y
```
