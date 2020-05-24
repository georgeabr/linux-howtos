## How to compile a vanilla kernel on Debian (with ZFS)

1. Download latest kernel from https://kernel.org.
2. Then untar `tar xf kernel.tar.xz` the downloaded kernel to `/usr/src`.
3. Install the debian packages `kernel-package`, `rsync`.
4. Add the line `"CONCURRENCY_LEVEL=9"` to file `/etc/kernel-pkg.conf`. It will use more threads to compile the kernel.
5. Copy a reference `.config` file from existing kernel;  
for example (copy `/boot/config-5.5.0-2-amd64` to `/usr/src/linux-5.6.8/.config`)
6. Run `sudo make menuconfig` from the new kernel folder. Change settings according to your cpu, hardware.
7. Run the below if you get `error 2` when compiling. `CONFIG_SYSTEM_TRUSTED_KEYS` should be blank:  
`sed -ri '/CONFIG_SYSTEM_TRUSTED_KEYS/s/=.+/=""/g' .config`
8. Run `sudo make-kpkg kernel_headers kernel_image`.  
Can try this, generates initial ramdisk also, and uses 9 tasks to build:  
`sudo make-kpkg -j$(( $(nproc) + 1 )) --initrd kernel_image kernel_headers`
9. The resulting 2 `.deb` packages are in `/usr/src`. Install them with `dpkg -i`.
10. Run the below commands:  
```
sudo dpkg-reconfigure zfs-dkms
sudo update-initramfs -u -k all
sudo update-grub
systemctl enable zfs-import-bpool.service
```
You can also consult these links for more information:  
<https://wiki.debian.org/BuildADebianKernelPackage>  
https://itbusters.wordpress.com/2013/02/07/how-to-build-debian-kernel-using-multiple-cores/
