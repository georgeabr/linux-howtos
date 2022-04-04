- Generate an ssh key as per below:  
```
ssh-keygen -t rsa -f ~/.ssh/gcloud_instance1 -C george
cat ~/.ssh/gcloud_instance1.pub
```
- Copy the contents of the `.pub` key.  
- Go to `Compute Engine -> Metadata`.  
- Select `SSH keys` tab and click `Edit` on the top menu.  
- Click on `Add item`. Paste the contents of the `.pub` key.  
- You should see the `username` appear on the left label. Then click `Save` at the bottom. 
