---
title: Ubuntu SAMBA
outdated: false
---

## Install and Configure Samba

### Overview

A Samba file server enables file sharing across different operating systems over a network. It lets you access your desktop files from a laptop and share files with Windows and macOS users.

This guide covers the installation and configuration of Samba on Ubuntu.

### Installing Samba

To install Samba, run:
```
sudo apt update
sudo apt install samba
```
check if the installation was successful by running:
```
whereis samba
```
!!! Info
    The following should be its output:
    ```
    samba: /usr/sbin/samba /usr/lib/samba /etc/samba /usr/share/samba /usr/share/man/man7/samba.7.gz /usr/share/man/man8/samba.8.gz
    ```
### Setting up Samba
Now that Samba is installed, you need to create a directory for it to share:
```
mkdir /home/<username>/smbshare/
```
The command above creates a new folder `smbshare` in our home directory which we will share later.

The configuration file for Samba is located at `/etc/samba/smb.conf`. To add the new directory as a share, you need to edit the file by running:
```
sudo nano /etc/samba/smb.conf
```
At the bottom of the file, add the following lines:
```
[smbshare]
    comment = Samba on Ubuntu
    path = /home/<username>/smbshare
    read only = no
    browsable = yes
    valid user = <username>
```
Then press `Ctrl-O` to save and `Ctrl-X` to exit from the nano text editor.

What we’ve just added

- comment: A brief description of the share.
- path: The directory of our share.
- read only: Permission to modify the contents of the share folder is only granted when the value of this directive is `no`.
- browsable: When set to `yes`, file managers such as Ubuntu’s default file manager will list this share under “Network” (it could also appear as browseable).
- valid user: user with write privilege for the share.

Now that you have your new share configured, save it and restart Samba for it to take effect:
```
sudo service smbd restart
```
Update the firewall rules to allow Samba traffic:
```
sudo ufw allow samba
```

### Setting up User Accounts and Connecting to Share
Since Samba doesn’t use the system account password, you need to set up a Samba password for your user account:
```
sudo smbpasswd -a <username>
```

!!! Warning
    Username used must belong to a system account, else it won’t save.

### Connect smbshares to respective service
