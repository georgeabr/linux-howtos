# Linux howtos
See each file for more information. 

### Hide the tab list arrow in Firefox
browser.tabs.firefox-view  
browser.tabs.tabmanager.enabled

### Fedora UEFI/bootloader
https://docs.fedoraproject.org/en-US/quick-docs/bootloading-with-grub2/#setting-default-entry
```
# fdisk -l | grep nvm
# mount root partition
# mount /dev/sda3 /mnt

# mount -o bind /dev /mnt/dev
# mount -o bind /proc /mnt/proc
# mount -o bind /sys /mnt/sys
# mount -o bind /run /mnt/run

# mount -o bind /sys/firmware/efi/efivars /mnt/sys/firmware/efi/efivars
# mount EFI partition
# mount /dev/sda1 /mnt/boot/efi

# chroot /mnt/

/]# dnf reinstall shim-* grub2-efi-* grub2-common

/]# grub2-mkconfig -o /boot/grub2/grub.cfg

/]# sync && exit
```

### Compile sway on Ubuntu/Debian
https://github.com/luispabon/sway-ubuntu/

### Screen sharing in sway
Add in file `~/.config/environment.d/sway.conf`, this entry `XDG_CURRENT_DESKTOP="${XDG_CURRENT_DESKTOP:-sway}"`   
These executables are needed - `xdg-desktop-portal`, `xdg-desktop-portal-wlr`.  
Run this (should not be needed, automatically ran): `/usr/libexec/xdg-desktop-portal -r & /usr/libexec/xdg-desktop-portal-wlr`.   
Consult on reddit [this link](https://www.reddit.com/r/swaywm/comments/l4e55v/guide_how_to_screenshare_from_chromiumfirefox/).  
Test with [this webrtc link](https://mozilla.github.io/webrtc-landing/gum_test.html).  
On Chrome, enable `enable-webrtc-pipewire-capturer` in `chrome://flags`.  

### Monospaced fonts  
https://input.fontbureau.com/  
https://www.fontsquirrel.com/fonts/cousine

### Chromium .deb Ubuntu
https://launchpad.net/~chromium-team/+archive/ubuntu/beta/+packages  
Use  
chromium-browser_84.0.4147.38-0ubuntu0.18.04.1_amd64.deb (64.7 MiB)  
chromium-codecs-ffmpeg-extra_84.0.4147.38-0ubuntu0.18.04.1_amd64.deb (1.1 MiB)  
chromium-codecs-ffmpeg_84.0.4147.38-0ubuntu0.18.04.1_amd64.deb (861.1 KiB)  

### Youtube volume normalisation - requires Tampermonkey browser extension
https://gist.github.com/abec2304/2782f4fc47f9d010dfaab00f25e69c8a

### Prefer external audio device on connect - Pulseaudio
https://askubuntu.com/questions/113704/make-pulseaudio-prefer-external-audio-device

### Good cursor theme - white or black
https://gitlab.com/Enthymeme/hackneyed-x11-cursors

### Calendar in console, start date is Monday
```
george@neon:~$ ncal -bM
```
```
   December 2020      
Mo Tu We Th Fr Sa Su  
    1  2  3  4  5  6  
 7  8  9 10 11 12 13  
14 15 16 17 18 19 20  
21 22 23 24 25 26 27  
28 29 30 31
```
