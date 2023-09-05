---
title: Connecting & Setting up with vps via ssh post creation
outdated: true
---


## Setting up

### Enable SSH Service (Do it in server side):
```
sudo apt install openssh-server
```

After installation, enable and start the service via command:
```
sudo systemctl enable ssh && sudo systemctl start ssh
```

And finally verify the SSH service status by running command:
```
sudo system status ssh
```
### Enable No Password SSH Key Login (Do in local PC):
You can tick `Automatically unlock this key whenever I’m logged in` and type the password only for one time in the last screenshot. However, some desktop environments may not provide this friendly feature. So `ssh-agent`, OpenSSH authentication agent, is present to do the job for your.

Firstly run `ssh-agent` via shell command:
```
eval 'ssh-agent'
```

Next, add the SSH key to the agent:
```
ssh-add <ssh key name>
```
After that, SSH command will login without typing the authentication key password.

### Disable SSH user password login (Do in server side):

After successfully setup the key authentication, you may disable the user password login, so no one else can access the server!

Firstly, connect to the remote server and run command to edit the ssh daemon config file:
```
sudo nano /etc/ssh/sshd_config
```

Next, un-comment the `#PasswordAuthentication yes` line and set its value to no, so it will be:

**PasswordAuthentication no**

And then press **Ctrl+X type y** and hit Enter to save the file.

Finally reload SSH via 

```
sudo systemctl reload ssh
```
## Enable passwordless sudo for ansible

### Allow passwordless sudo

Manually editing the sudoers
`ssh` to the remote server.

```
ssh <username>@<ip.address>
```
On the remote server run:
```
sudo visudo
```
We need to edit the line
```sudo   ALL=(ALL:ALL) ALL```  to  ```sudo  ALL=(ALL:ALL) NOPASSWD: ALL```
Save file and exit
