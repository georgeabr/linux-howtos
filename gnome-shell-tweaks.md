# How to do stuff

## Screen tearing in gnome shell
You must enable intel `TearFree` option in xorg conf file. Then install the extension below:  
https://github.com/kazysmaster/gnome-shell-extension-disable-unredirect

## Change the top bar font
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
3. Launch `Gnome Tweaks` and apply the new shell theme.
