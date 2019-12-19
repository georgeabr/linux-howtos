## Use a proper mirror for `debootstrap`
When using debootstrap, choose this mirror  
http://mirror.asergo.com/debian/

## To speed up `debootstrap`
```
apt install eatmydata  
exec 3< /usr/lib/x86_64-linux-gnu/libeatmydata.so  
LD_PRELOAD=/proc/$$/fd/3 debootstrap bullseye /mnt  
```
