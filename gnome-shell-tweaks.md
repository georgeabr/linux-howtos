# How to customise your gnome shell appearance

## Cannot enable gnome shell extensions  
https://unix.stackexchange.com/questions/368306/cannot-re-renable-gnome-extensions

## Screen tearing in gnome shell (tested on ver. 3.34)
You must enable intel `TearFree` option in xorg conf file. Then install the extension below:  
https://github.com/kazysmaster/gnome-shell-extension-disable-unredirect

## Change shortcuts for switching workspace
If you want to use `Win+1/2/3` to move between workspaces, first clear these shortcuts.  
Gnome shell uses them to switch between open applications.
```
gsettings set org.gnome.shell.keybindings switch-to-application-1 []
gsettings set org.gnome.shell.keybindings switch-to-application-2 []
gsettings set org.gnome.shell.keybindings switch-to-application-3 []
gsettings set org.gnome.shell.keybindings switch-to-application-4 []
```

## Change the top bar font
You need the `User themes` extension installed.
1. Create and edit a new gnome shell theme:
```
mkdir -p ~/.themes/ModMyTheme/gnome-shell; nano ~/.themes/ModMyTheme/gnome-shell/gnome-shell.css
```
2. Add the following lines in `css` file, to change font:
```
@import url("resource:///org/gnome/theme/gnome-shell.css");

stage {
    font-family: Roboto Condensed;
    font-size: 15px;
}
```
3. Launch `Gnome Tweaks` and apply the new **shell** theme.

## Interesting gnome shell extensions
1. **Time++** - todo manager  
https://github.com/zagortenay333/timepp__gnome
2. **Unite** - Removes titlebars on maximized windows, shows title in gnome top bar, with close/maximise buttons  
https://github.com/hardpixel/unite-shell
3. **Gtile** - A window tiling extension for Gnome  
https://github.com/gTile
4. **Remove Audio Device Selection Dialog**  
https://extensions.gnome.org/extension/1482/remove-audio-device-selection-dialog/
For **Remove Audio Device Selection Dialog**, do the below to make it remember the headphones:
```
    sudo nano /etc/pulse/default.pa
```
Find and comment out the following line (#):
```
    load-module module-switch-on-port-available
```
Now restart PulseAudio
```
    pulseaudio -k
```

5. **Dash to dock**  
https://micheleg.github.io/dash-to-dock/
6. **Clock override** - change top bar clock date/time format  
https://github.com/stuartlangridge/gnome-shell-clock-override
7. **Simple task bar** - icons of running applications on Gnome shell's top bar  
https://github.com/fthx/simple-task-bar  
To make the icons grayscale, edit the file `extension.js`, in the `for` loop below this comment `// create all normal windows icons and buttons`:
```
gedit ~/.local/share/gnome-shell/extensions/simple-task-bar\@fthx/extension.js
```
```
	        let iconEffect = new Clutter.DesaturateEffect();
                box.add_effect(iconEffect);
                box.icon.add_effect(iconEffect);
```

## Change Gnome shell's top bar font, colour
To change the colour or font of the Gnome shell top bar, run the below:
```
mkdir -p ~/.local/share/themes/custom/gnome-shell/
nano ~/.local/share/themes/custom/gnome-shell/gnome-shell.css
```

Add the following text to the file, customise as needed:
```
@import url("resource:///org/gnome/theme/gnome-shell.css");

stage {
    font-family: "Roboto Mono", Sans-Serif;
    font-size: 11pt;
}

#panel {
/*
background-color: #140f03;
*/

background-gradient-direction: vertical;
background-gradient-start: #1c1b1b;
background-gradient-end: #0e0e0e;
text-shadow: 0px 0px 2px rgba(0, 0, 0, 0.9);
```
