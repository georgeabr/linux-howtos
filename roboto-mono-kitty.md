Some mono fonts are fucked. Use this to fix them. They will show as monospace in applications:  

Add these lines to `~/.config/fontconfig/fonts.conf`:  
```
<match target="font">
    <test name="family">
    <string>Roboto Mono</string>
    </test>
    <edit name="spacing">
        <int>100</int>
    </edit>
</match>
```
Update the font cache:  
`fc-cache -r`

Or use
https://github.com/haasosaurus/nerd-fonts/blob/regen-mono-font-fix/patched-fonts/RobotoMono/Regular/complete/Roboto%20Mono%20Nerd%20Font%20Complete%20Mono.ttf
