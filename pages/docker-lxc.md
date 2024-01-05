---
layout: page
title: Docker-LXC
permalink: /homelab/docker-lxc/
parent: Homelab
---

# Docker setup on Debian LXC

## Installing Docker

Install Docker, containerd and Docker Compose from Dockers Debian repositories. https://docs.docker.com/engine/install/debian/

Installing the prerequisites:

```
sudo apt-get install ca-certificates curl gnupg
```

Setting up the apt repositories and keyrings:

```
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg
```

```
> echo \
  "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

Installing Docker, containerd and Docker Compose:

```
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```