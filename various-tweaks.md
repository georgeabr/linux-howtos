Assign a the below command to a custom shortcut (Win+1), to switch to another workspace.  
This is handy if you use `xfwm4 --replace` as a compositor in Mate.  
`compton` or `picom` have high CPU usage (around 6%).
```
wmctrl -s 0
```
