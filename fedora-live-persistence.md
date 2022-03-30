## Fedora live USB with persistence (root drive and home folder overlay)
```
git clone https://github.com/livecd-tools/livecd-tools.git
cd livecd-tools/tools/
sudo ./livecd-iso-to-disk.sh --overlay-size-mb 14094 --home-size-mb 4094 \
--unencrypted-home --format --efi \
~/Downloads/Fedora-Workstation-Live-x86_64-36-20220329.n.1.iso /dev/sda
```
