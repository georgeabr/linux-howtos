# How to customise your gnome shell appearance

## Screen tearing in gnome shell
You must enable intel `TearFree` option in xorg conf file. Then install the extension below:  
https://github.com/kazysmaster/gnome-shell-extension-disable-unredirect

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
5. **Dash to dock**  
https://micheleg.github.io/dash-to-dock/
6. **Clock override** - change top bar clock date/time format  
https://github.com/stuartlangridge/gnome-shell-clock-override
