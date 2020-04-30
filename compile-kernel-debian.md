1. Download latest kernel from kernel.org
2. untar the downloaded kernel to `/usr/src`.
3. Install the debian package `kernel-package`.
4. Add the line `"CONCURRENCY_LEVEL=9"` to file `/etc/kernel-pkg.conf`. It will use more threads to compile the kernel.
5. Copy a reference `.config` file from existing kernel, for example ( copy `/boot/config-5.5.0-2-amd64` to `/usr/src/linux-5.6.8/.config`
