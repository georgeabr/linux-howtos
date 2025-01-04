Download a __standard__ Debian ISO (no GUI, debian-live-testing-amd64-standard.iso) from [here](https://cdimage.debian.org/cdimage/weekly-live-builds/amd64/iso-hybrid/?C=S;O=D).  
Create a Virtualbox VM, enable UEFI, a 18GB disk size, with the ISO as boot.  
Set the networking to __Bridged adapter__ in Virtualbox, so that you get an IP address from the router and can access internet. NAT doesn't seem to work.  
Start the VM, choose the live boot, install ssh server `sudo apt install openssh-server`.  
Add a password to the `user` account `sudo passwd user`. You will need it to connect to the VM via ssh from your laptop.  
Connect to the Debian VM via ssh - `ssh user@192.168.1.101`.  
