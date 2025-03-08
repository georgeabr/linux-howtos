### VirtualBox theming with QT6

Install `qt6ct` from your distribution repos.  
Make sure `Gnome Shell` is installed.  
Create a `bash` file:
```bash
#!/bin/sh
/usr/libexec/gsd-xsettings &
sleep 1
QT_QPA_PLATFORMTHEME=gtk2 VirtualBox
```
