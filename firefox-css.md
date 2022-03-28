Create a file in your `about:profile` profile, in the `chrome` folder - name `userChrome.css`  
Enable in `about:config` the following -`toolkit.legacyUserProfileCustomizations.stylesheets` = `true`.  
@namespace url("http://www.mozilla.org/keymaster/gatekeeper/there.is.only.xul"); /* only needed once */  

```
/* hide 'overlink' messages */  
#statuspanel[type="overLink"] {opacity: 0 !important;}  
/* hide all status messages */  
#statuspanel {opacity: 0 !important;}  
```
