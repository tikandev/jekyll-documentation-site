---
layout: page
title: Ultimate LXC with Docker and NAS
permalink: /homelab/ultimate_lxc/
parent: Homelab
---


# The ultimate LXC with Docker and NAS

Earlier I had created two different containers for running Docker and NAS on Proxmox. These both are fine solutions and they could be used separately. However if there's need and will to have Docker use the same ZFS pools for storage, there will be a hassle. Though there are tutorials for accomplishing that, I myself will try another route. So in this document I will explore if I can combine the two LXCs to one and create a monster that is the "Ultimate DOCKER/NAS LXC"™. Disclaimer: this is not the smartest or the most secure way of doing this. But it's used in my Homelab which can be only accessed within my own home, so I can deem it safe enough for my use case.

## Creating the container

