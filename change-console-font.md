Edit the settings, `nano /etc/default/console-setup` file.  

Debian man page for `console-setup`:  
https://manpages.debian.org/testing/console-setup/console-setup.5.en.html
```
# Consult the console-setup(5) manual page.

ACTIVE_CONSOLES="/dev/tty[1-6]"

CHARMAP="UTF-8"

CODESET="guess"
FONTFACE="Fixed"
FONTSIZE="8x16"

VIDEOMODE=
```

Possible values:  
```
  FONTFACE and FONTSIZE
          Valid font faces are: 
          VGA               (sizes 8x8, 8x14, 8x16, 16x28 and 16x32),  
          Terminus          (sizes 6x12, 8x14, 8x16, 10x20, 12x24, 14x28 and 16x32), 
          TerminusBold      (sizes 8x14, 8x16, 10x20, 12x24, 14x28 and 16x32),  
          TerminusBoldVGA   (sizes 8x14 and 8x16), 
          Fixed             (sizes 8x13, 8x14, 8x15, 8x16 and 8x18).    
 ```
