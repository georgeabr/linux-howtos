Create a file in your `about:profile` profile, in the `chrome` folder - name `userChrome.css`  
Enable in `about:config` the following -`toolkit.legacyUserProfileCustomizations.stylesheets` = `true`.  
```
@namespace url("http://www.mozilla.org/keymaster/gatekeeper/there.is.only.xul"); /* only needed once */  

/* hide sidebar titlebar */  
#sidebar-box #sidebar-header {  display: none !important;}

/* hide the tab bar at the top, if you have treestyletab */  
#tabbrowser-tabs { visibility: collapse !important; }

/* hide 'overlink' messages, disabled it to show tooltips */ 
/*
#statuspanel[type="overLink"] {opacity: 0 !important;}  
*/

/* hide all status messages */  
#statuspanel {opacity: 0 !important;}  
```

Better   
```
/** Import Lepton stylesheet (Content) **********
userContent.css
******************************/
@import url(
"
css/leptonContent.css
"
);
/** Add your custom styles below **********************************************/
* { scrollbar-width: none }
```

```
/** Import Lepton stylesheet (Chrome) *************************************
userChrome.css
NEW PROFILE
****/
@import url(
"
css/leptonChrome.css
"
);
@namespace url(
"
http://www.mozilla.org/keymaster/gatekeeper/there.is.only.xul
"
); /* only needed once */
/** Add your custom styles below **********************************************/
/* Enable option to edit bookmark URLs under Add Bookmark (blue star) menu */
/*
 * #editBMPanel_locationRow {
 *
 visibility: visible !important;
 * }
*/
/* Disable Email Image/Audio/Video and Set as Desktop Background context menu items */
/*
 * #context-sendimage,
 * #context-sendvideo,
 * #context-sendaudio,
 * #context-setDesktopBackground {
 *
 display: none !important;
 * }
*/
#statuspanel #statuspanel-label {
display:none!important;
}
#statuspanel {
display:none!important;
}
statuspanel { display:none!important; }
#statuspanel[type=
"
overLink
"
] #statuspanel-label {
 display:none!important;
}
#statuspanel[type=
"
status
"
] #statuspanel-label[value^=
"
Look
"
],
#statuspanel[type=
"
status
"
] #statuspanel-label[value^=
"
Connect
"
],
#statuspanel[type=
"
status
"
] #statuspanel-label[value^=
"
Send
"
],
#statuspanel[type=
"
status
"
] #statuspanel-label[value^=
"
Transfer
"
],
#statuspanel[type=
"
status
"
] #statuspanel-label[value^=
"
Read
"
],
#statuspanel[type=
"
status
"
] #statuspanel-label[value^=
"
Wrote
"
],
#statuspanel[type=
"
status
"
] #statuspanel-label[value^=
"
Wait
"
],
#statuspanel[type=
"
status
"
] #statuspanel-label[value*=
"
TLS handshake
"
],
#statuspanel[type=
"
status
"
] #statuspanel-label[value*=
"
FTP transaction
"
] {
 display:none!important;
}
#sidebar-box #sidebar-header {
 display: none !important;
}
/* hides the native tabs */
#TabsToolbar {
 visibility: collapse;
}
```
