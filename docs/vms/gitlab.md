---
title: Gitlab CE
outdated: false
---

## Setting up gitlab in ubuntu 22.04 LTS

1. Update system & install dependencies
```bash
sudo apt update
sudo apt upgrade -y
```
Install GitLab dependencies below:
```bash
sudo apt install -y ca-certificates curl openssh-server tzdata
```

2. Configure Postfix Send-Only SMTP server<br>
[How to setup send-only SMTP server](/others/set-up-sent-only-smtp-server)
3. Add the GitLab CE Repository<br>
After all pre-requisites installed, proceed to GitLab repository to ubuntu.<br>
Install dependency pacckages:
```bash
sudo apt install curl debian-archive-keyring lsb-release ca-certificates apt-transport-https software-properties-common -y
```
Import GitLab repo GPG key:
```bash
gpg_key_url="https://packages.gitlab.com/gitlab/gitlab-ce/gpgkey"
curl -fsSL $gpg_key_url| sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/gitlab.gpg
```
Add repository contents to `/etc/apt/sources.list.d/gitlab_gitlab-ce.list`file.
```bash
sudo tee /etc/apt/sources.list.d/gitlab_gitlab-ce.list<<EOF
deb https://packages.gitlab.com/gitlab/gitlab-ce/ubuntu/ focal main
deb-src https://packages.gitlab.com/gitlab/gitlab-ce/ubuntu/ focal main
EOF
```
Confirm configured repository is working by updating APT packagee index.
```bash
sudo apt update
```
4. Install GitLab CE<br>
Once the reposittory using the `apt` package manager command:
```bash
sudo apt update
sudo apt install gitlab-ce
```
Edit GitLab configuration file to set *hostname* and other parameters:
```bash
sudo nano /etc/gitlab/gitlab.rb
```
Start GitLab instance by running the following command:
```bash
sudo gitlab-ctl reconfigure
```

5. Access GitLab CE Web Interface<br>
Open URL `http://gitlab.example.com` on your browser to finish installation of GitLab.<br>
A password for root user is randomly generated and stored for 24 hours in `/etc/gitlab/initial_root_password`.
```bash
cat /etc/gitlab/inittial_root_password
```
Use password with username `root` to login

6. Reset root user password
Go to root user profile > *Preferences* then *Password* section to change it.

## GitLab Administrations cheats

- Whenever GitLab configuration file is editted `/etc/gitlab/gitlab.rb`, reconfigure GitLab seervice by running:
```bash
sudo gitlab-ctl reconfigure
sudo gitlab-rake gitlab:check
```

- To check the status of all GitLab service, use:
```bash
sudo gitlab-ctl status
```

- To stop all GitLab services, use:
```bash
sudo gitlab-ctl stop
```

- To restart all GitLab services, use:
```bash
sudo gitlab-ctl restart
```

- To restart a specific service by proving service name at the end:
```bash
sudo gitlab-ctl restart logrotate
```
