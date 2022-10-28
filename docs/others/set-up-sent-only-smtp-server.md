---
title: SETUP SENT ONLY SMTP SERVER USING POSTFIX
outdated: true
---
### Configure poistfix as a send-only SMTP Server on Ubuntu 22.04

- Update system
```bash
sudo apt update
```
- Set hostnaame for the server so that emails will show a from address with valid domain section
```bash
sudo hostnamectl set-hostname server.example.com
```
- Download mailutils which install postfix and other mail utils for you:
```bash
sudo apt install mailutils
```
- Follow screen opttion for mail server. For `General type of email configuration` window select `internet site` and click `OK` button.
- Set mail server name
```bash
server.example.com
```

### Configure postfix MTA Server
- Edit postfix configuration file `/etc/postfix/main.cf` to ensure it is configured as send only (Only relaying emails from the local server).
- Set postfix to listen on the `127.0.0.1` loopback interface.
- Set `inet_interfaces = loopback-only`
- Change `hostname` to server FQDN
```bash
myhostname=server.example.com
```
- Restart Postfix service for it too pick the new changes:
```bash
sudo systemctl restart postfix
```
- Perform test to ensure proper delivery
```bash
echo "Postfix Send-Only Server" | mail -s "Postfix Testing" userx@example.com
```
