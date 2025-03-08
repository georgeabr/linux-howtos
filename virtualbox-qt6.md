### VirtualBox theming with QT6

Install `qt6ct` from your distribution repos.  
Make sure `Gnome Shell` is installed.  
Create a `bash` file `/usr/sbin/virtualbox-non-kde`, make it executable:
```bash
#!/bin/sh
/usr/libexec/gsd-xsettings &
sleep 1
QT_QPA_PLATFORMTHEME=gtk2 VirtualBox
```
You can create a `.desktop` entry for it:
```
[Desktop Entry]
Name=VirtualBox Non KDE
Exec="/usr/sbin/virtualbox-non-kde"
Type=Application
StartupNotify=true
Comment=Run Virtualbox
Icon=/usr/share/icons/hicolor/128x128/apps/virtualbox.png
```
You can create a shortcut for it:
```bash
sudo ln -s /home/george/Desktop/virtualbox-non-kde.desktop /usr/share/applications/virtualbox-non-kde.desktop
```
