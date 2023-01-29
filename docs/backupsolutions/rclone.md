---
title: Rclone
outdated: true
---

### Installing and configuring rclone

- ssh into shell and start a root session
```
sudo -i
```
- Download & Install rclone
```
curl https://rclone.org/install.sh | sudo bash
```
- Configure
```
rclone config
```

- Options to choose to setup Google Drive
```
n for new remotes
Gdrive for name
18 for Google Drive
Paste in your Client ID (hit enter)
Paste in your Client Secret (hit enter)
Option 1 (some people have said option 3 works just as well)
Leave root_folder_id and service_account_file blank (hit enter to leave blank on both)
n to skip advanced config
n for autoconfig
Copy the link to a browser to create a token, then approve in the Google authentication screen (click Advanced, then click Go to TeslaMate (or whatever you called the service)) then click Allow on the popup, then Allow again for the Confirm your choices screen.
Copy the verification code and paste into your SSH session.
n for team drive
y to confirm
q then enter to quit config
```
### Export Drive

- Export drive
```
RCLONE_DRIVE="Gdrive"
```
- Verify Gdrive was created
```
rclone listremotes
```
??? info

    There should be Gdrive, if not you’ll need to start the rclone config process again as something has gone wrong.

- list rclone directory
```
rclone lsd "$RCLONE_DRIVE":
```
- There should be no files showing.
- Choose a folder to copy the backup files to 
```
export RCLONE_PATH="Backup"
```
- Make a folder in Google Drive with folder name
```
rclone mkdir "$RCLONE_DRIVE:Backup"
```
- Check if folder was created
```
rclone lsd "$RCLONE_DRIVE":
```
- This step might not be needed for our purposes, but I ran it just in case: 
```
export ARCHIVE_SYSTEM=rclone
```

### Create Script for Autobackup
- Staying in the sudo -i session
- Navigate to the folder where backup is store
- Create a script in the folder
```
nano backup.sh
```


