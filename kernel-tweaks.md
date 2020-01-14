
### Choppy performance 
It manifests itself while downloading large files (especially during their decompression) and watching video files at the same time.  
Add the following in `/etc/sysctl.conf`:

```
vm.dirty_bytes = 16777216
vm.dirty_background_bytes = 4194304
```
Then run to apply immediately
```
sudo sysctl -p
```
