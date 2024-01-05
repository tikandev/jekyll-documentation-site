---
layout: page
title: Proxmox Share
permalink: /homelab/proxmox-share/
parent: Homelab
---

# Proxmox share

This outlines the steps of getting a SMB share working in LXC containers on Docker.


## Creating a Debian LXC

First we need to create a Linux Container (LXC). Virtually any distro can be used as a base, but for the purposes of this document Debian 12 will be used.

Click Create CT and fill in the information. 'Unprivileged container' option must be **unchecked**. This is because LXCs aren't permitted to use or access SMB/CIFS otherwise.

After the LXC is created we need to enable couple of options before we start the container for the first time. From container options select Features and Edit. We need to check 'Nesting' and 'SMB/CIFS'. 

## Setting up the Debian LXC

Now on to the first boot of Debian LXC. You will be greeted with the shell login screen, you can log in as root with the password you set during the creation of the LXC.

First we need to run the commands commands to bring the os up-to-date.

``` 
apt update && apt upgrade -y 
```

This Debian image doesn't have 'sudo' already installed, so next we need to install that.

``` 
apt install sudo
```

Next we create our user and add it to group 'sudo'.

```
adduser your_username
usermod -aG sudo your_username
```

Now we can reboot and login with our newly created user. To test if our user has the correct rights we can run 'sudo whoami' which should ask for a password and then print 'root'.

## Setting up the SMB

To start using SMB shares on the container we need to get correct prerequisites installed first. On Debian the package which contains everything we need is called 'cifs-utils'. So let's install that.

``` 
sudo apt install cifs-utils
```

Shares also need some place to mount to. So let's create a directory for that.

```
sudo mkdir /mnt/yourshare
```

Next let's create a place to store our credentials for the share. Good place to store them would be users home folder. We can make the file hidden by adding '.' in front of the filename.

``` 
nano /home/yourusername/.smbcredentials
```

In the credentials file we declare the username and password used to mount the share.

``` 
username=your_username
password=your_password
```

Now we are ready to try and mount the share for the first time. Here we are telling the system to mount SMB share as cifs, where the share is located and where it should be mounted. Lastly '-o credentials=' tells the mount to use the credentials we just saved to the '.smbcredentials' file.

``` 
sudo mount -t cifs //yourserver/yourshare /mnt/yourshare -o credentials=/home/yourusername/.smbcredentials
```

Your file should now be mounted! You can check that everything is working correctly with the command:

```
ls /mnt/yourshare
```

If you can see files and directories listed from your share then all is good. The share will be mounted only until the next boot though and that's what we will be fixing in the next section.

## Getting the SMB Share to mount

One way to get the share to automatically mount on boot is to edit the 'fstab' found on the system.
Editing the fstab so the SMB share will automount on boot.

```
sudo nano /etc/fstab
```

Write on a new line in the file:


``` 
//your-server-ip/your-share /mnt/your-share cifs credentials=/home/yourusername/.smbcredentials,uid=1000,gid=1000,auto 0 0
```

First part tells us which share we are mounting and the second tells us where it will be mounted. Then we declare the mounts filesystem to be *cifs*. Next part contains the options for the mount. 

credentials=/root/smbcredentials informs the mounting process to use the credentials stored in the file created earlier.

'uid=1000,gid=1000' tell what user rights the mount should have, in this case the user 'vernu'.

'auto' tells the system to mount automatically. Using Systemd method here with x-systemd.automount would be great, but it's usage is limited on containers. They rely on the underlying kernels 'autofs' module which the containers don't have access to. Therefore we use regular option 'auto' instead.