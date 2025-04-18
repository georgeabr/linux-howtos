### Enable promiscuous mode in VMware
This will allow VMs in nested ESXi instances to get an IP address.  
The scenario: laptop => VMware => ESXi => VMs.  

Create `vmware-promisc.sh` in your home folder.
```bash
#!/bin/bash

/bin/chmod 666 /dev/vmnet1
/bin/chmod 666 /dev/vmnet8
```
Make it executable and SELinux compliant:
```bash
chmod +x vmware-promisc.sh
sudo chcon -t bin_t vmware-promisc.sh
```
Create the systemd service `/etc/systemd/system/vmware-promisc.service`:
```
[Unit]
Description=VMware - enable promiscuous mode for all users to vmnet1 and vmnet8
After=vmware.service

[Service]
ExecStart=/home/george/vmware-promisc.sh
Restart=on-failure

[Install]
WantedBy=multi-user.target
```
Reload systemd:
```bash
sudo systemctl daemon-reload
```
Enable and start the service:
```bash
sudo systemctl enable --now vmware-promisc.service
```
Check if all is well with `systemctl status vmware-promisc.service`:
```bash
$ systemctl status vmware-promisc.service
○ vmware-promisc.service - VMware - enable promiscuous mode for all users to vmnet1 and vmnet8
     Loaded: loaded (/etc/systemd/system/vmware-promisc.service; enabled; preset: disabled)
    Drop-In: /usr/lib/systemd/system/service.d
             └─10-timeout-abort.conf, 50-keep-warm.conf
     Active: inactive (dead) since Fri 2025-04-18 11:37:23 BST; 1s ago
   Duration: 27ms
 Invocation: 68571817553b4451957ac03941407d5d
    Process: 11312 ExecStart=/home/george/vmware-promisc.sh (code=exited, status=0/SUCCESS)
   Main PID: 11312 (code=exited, status=0/SUCCESS)
   Mem peak: 1.3M
        CPU: 6ms

Apr 18 11:37:23 fedora systemd[1]: Started vmware-promisc.service - VMware - enable promiscuous mode for all users to vmnet1 and vmnet8.
Apr 18 11:37:23 fedora systemd[1]: vmware-promisc.service: Deactivated successfully.
```
