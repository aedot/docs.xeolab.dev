---
title: Installing docker
outdated: false
---

## Installing Docker

The Docker installation package available in the official Ubuntu repository may not be the latest version. To ensure we get the latest version, we’ll install Docker from the official Docker repository. To do that, we’ll add a new package source, add the GPG key from Docker to ensure the downloads are valid, and then install the package.

- Update your existing list of packages:
```
sudo apt-get update
```
- Install prerequisite packages which let apt use packages over HTTPS:
```
sudo apt-get install apt-transport-https ca-certificates curl gnupg lsb-releas
```
- Then add the GPG key for the official Docker repository to your system
```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```
- Add Docker repository to APT sources:
```
 echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```
- Update package database with Docker packages from the newly added repo
```
sudo apt-get update
```
- Install Docker
```
sudo apt-get install docker-ce docker-ce-cli containerd.io
```
- Check Docker version
```
docker -v
```
- Install Docker compose
```
sudo curl -L "https://github.com/docker/compose/releases/download/v2.12.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```
- Change permission
```
sudo chmod +x /usr/local/bin/docker-compose
```
- Check installation
```
docker-compose -v
```
- To use Docker without sudo
```
sudo usermod -aG docker $USER
```
## To uninstall docker
```
sudo apt-get remove docker docker-engine docker.io containerd runc
```
