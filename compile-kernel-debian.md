1. Download latest kernel from https://kernel.org.
2. untar the downloaded kernel to `/usr/src`.
3. Install the debian packages `kernel-package`, `rsync`.
4. Add the line `"CONCURRENCY_LEVEL=9"` to file `/etc/kernel-pkg.conf`. It will use more threads to compile the kernel.
5. Copy a reference `.config` file from existing kernel, for example ( copy `/boot/config-5.5.0-2-amd64` to `/usr/src/linux-5.6.8/.config`
6. Run `sudo menuconfig` from the new kernel folder. Change settings according to your cpu, hardware.
7. Run the below if you get `error 2`. `CONFIG_SYSTEM_TRUSTED_KEYS` should be blank:  
`sed -ri '/CONFIG_SYSTEM_TRUSTED_KEYS/s/=.+/=""/g' .config`
8. Run `sudo make-kpkg kernel_headers`, to generate `kernel headers`.
9. Run `sudo make-kpkg kernel_image` to generate `kernel image`.
10. Install the resulting 2 packages with `dpkg -i`.
11. Run the below commands:
```
sudo dpkg-reconfigure zfs-dkms
sudo update-initramfs -u -k all
sudo update-grub
systemctl enable zfs-import-bpool.service
```
You can also consult this link for more information:  
<https://wiki.debian.org/BuildADebianKernelPackage>
