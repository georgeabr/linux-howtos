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

TreeStyleTab
```
/* Better alignment of Favicons when collapsed */
tab-item[data-level][data-level="1"] tab-item-substance {
  margin-left: 4px !important;
}
tab-item[data-level][data-level="2"] tab-item-substance {
  margin-left: 8px !important;
}
tab-item[data-level][data-level="3"] tab-item-substance {
  margin-left: 12px !important;
}
tab-item[data-level][data-level="4"] tab-item-substance {
  margin-left: 16px !important;
}
tab-item[data-level][data-level="5"] tab-item-substance {
  margin-left: 20px !important;
}
tab-item[data-level][data-level="6"] tab-item-substance {
  margin-left: 24px !important;
}
tab-item[data-level][data-level="7"] tab-item-substance {
  margin-left: 28px !important;
}
tab-item[data-level][data-level="8"] tab-item-substance {
  margin-left: 32px !important;
}
tab-item[data-level][data-level="9"] tab-item-substance {
  margin-left: 36px !important;
}
/* hides the native tabs */
#TabsToolbar {
  visibility: collapse;
}

.tab.loading:not(.collapsed) .throbber::before,
:root.blocking-throbber #blocking-screen .throbber::before {
  animation: throbber 1s frames(2) infinite;
}

.throbber::before {
  box-shadow: none;
  border-radius: 0;
  border-width: 3px
}

@keyframes throbber {
  from { transform: translateX(0px) }
}

.tab {
  height: 24px;
background-color: #light-gray;
}

.tab .twisty {
  margin-right: -1em;
  opacity: 0;
  position: relative;
  z-index: 10000;
}

.label {
font-size: 11px;
/*font-family: Inconsolata;*/
font-weight: 600;
}

.tab:not(:hover) .closebox {
  display: none;
}


/****/
:root,
#background {
  background: black;
}

.tab
{background: light-gray;}  


.newtab-button-box {
display: none;
}
```
