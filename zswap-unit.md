## How to use `zswap` with a systemd unit
1. Create a systemd unit:  
`
sudo nano /etc/systemd/system/zswap.service
`

2. Add the following lines:  
```
[Unit]
Description=Zswap Memory Compression

[Service]
Type=oneshot
RemainAfterExit=true
ExecStart=/bin/sh -c "\
 modprobe z3fold;\
 echo Y > /sys/module/zswap/parameters/same_filled_pages_enabled;\
 echo 25 > /sys/module/zswap/parameters/max_pool_percent;\
 echo zstd > /sys/module/zswap/parameters/compressor;\
 echo zsmalloc > /sys/module/zswap/parameters/zpool;\
 echo z3fold > /sys/module/zswap/parameters/zpool;\
 echo Y > /sys/module/zswap/parameters/enabled;\
 echo 25 > /proc/sys/vm/swappiness"
ExecStop=/bin/sh -c "\
 echo 25 > /proc/sys/vm/swappiness;\
 echo N > /sys/module/zswap/parameters/enabled"

[Install]
WantedBy=multi-user.target
```

3. Enable the systemd unit:  
`
sudo systemctl enable zswap.service
`

4. Start the systemd unit:  
`
sudo systemctl enable zswap.service
`

5. See log files for the unit:  
`
journalctl -f zswap
`

6. See status of unit:  
`
sudo systemctl status zswap.service
`

7. To see the `zswap` parameters at runtime:  
`
grep -R . /sys/module/zswap/parameters
`
