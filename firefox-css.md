Create a file in your `about:profile` profile, in the `chrome` folder - name `userChrome.css`  
Enable in `about:config` the following -`toolkit.legacyUserProfileCustomizations.stylesheets` = `true`.  
```
@namespace url("http://www.mozilla.org/keymaster/gatekeeper/there.is.only.xul"); /* only needed once */  

/* hide sidebar titlebar */  
#sidebar-box #sidebar-header {  display: none !important;}

/* hide the tab bar at the top, if you have treestyletab */  
#tabbrowser-tabs { visibility: collapse !important; }

/* hide 'overlink' messages */  
#statuspanel[type="overLink"] {opacity: 0 !important;}  
/* hide all status messages */  
#statuspanel {opacity: 0 !important;}  
```
