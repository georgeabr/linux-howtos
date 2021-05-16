### Copy ssh key to droplet
To copy a ssh key to a DO droplet, you temporarily enable password login for the `sshd` daemon.   

Log in as `root`:  
Edit ssh config:  
```
sudo nano /etc/ssh/sshd_config
```

Change this line:   
```
PasswordAuthentication no
```
to
```
PasswordAuthentication yes
```
Restart daemon:  
```
sudo systemctl restart sshd
```

Then run the below to copy the generated ssh key to the acount named `test`.  
Note that ssh port is `5225`.  
You will be prompted for the UNIX password for user `test`:  
```
ssh-copy-id -p 5225 -i .ssh/id_rsa.pub test@domain.co.uk
```

Then revert the ssh daemon changes by changing the ssh config line to:  
```
PasswordAuthentication no
```
Restart daemon:  
```
sudo systemctl restart sshd
```

To connect to the droplet, with port `5225`:  
```
ssh test@domain.co.uk -p 5225
```

More information at [DigitalOcean](https://www.digitalocean.com/community/questions/ssh-copy-id-not-working-permission-denied-publickey).
