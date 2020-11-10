# Raspberry Pi Docker-server

![raspberry pi](IMG/a999bb5f-Raspberry+Pi+4.webp)

## Intro

> This document is my personal log of making a home server with a raspberry pi 4 (4GB model).
> If you like to know more about docker, what it is and how it works i recommend to watch this video: [Andreas Spiess: #295 Raspberry Pi Server based on Docker, with VPN, Dropbox backup, Influx, Grafana, etc.](https://www.youtube.com/watch?v=a6mjt8tWUws). It explaines in a simple way what docker is, how it works and how you can use it. It is only 18 minutes and i think it is one of best video's that explaines in a short way what docker is, how it works and how you can use it.

## Why making a server with docker, a raspberry pi and ubuntu?

> I believe docker is a usefull skill and tool in any development environment. I document my steps to learn how to work with docker, docker-compose and ubuntu.


## installation

### Install ubuntu

1. [Download](https://ubuntu.com/download/raspberry-pi) the newest ubuntu raspberry pi image. (Use an image that is newer than ubuntu 19.10. _Older versions don't support 4GB raspberry pi model_)
> Update: Use the raspberry pi imager. It is the latest tool from the raspberry pi foundation to make a raspbian image or ubuntu image for your raspberry pi
[Raspberry Pi Imager](https://www.raspberrypi.org/blog/raspberry-pi-imager-imaging-utility/)

2. Write an image to an sd-card. I use the disks application in ubuntu to write an image to an sd-card. When i open up the disks application an select the sd-card on the left pane. On the left of the minimize button you wille a 3 dotted button, select this to see the drive options menu. In the drive option menu select the "restore disk image option." Select the image you downloaded from step 1 to flash the sd card. If you would like to know more about flashing an sd card with an operatingsystem you can look at the [OMV installationguide](https://github.com/OpenMediaVault-Plugin-Developers/docs/blob/master/Adden-B-Installing_OMV5_on_an%20R-PI.pdf). They have an extensive document about installing omv and the first chapter they explain a lot about how to test the integrity of you sd-card with and how to make sure the disk is going to work properly on your system. If you don't like reading you can do a quick setup with this video from [ETA-prime](https://www.youtube.com/watch?v=HMo9C7LCzE0)

3. Before you put you sd-card in your raspberry pi to boot ubuntu you first have to edit some configuration files. Go to disks application and mount the "boot" partition. create an empty file without any extension and name it "SSH". This will enable ssh on boot, so you are able to use ssh. I will use a ethernet cable to attach my raspberry pi to my home network. In this partition you will find a readme with information about config.txt. A raspberry pi doesn't come with a nice graphical interface. You need to edit config.txt to make changes in the hardware interface of the device. Use the following lines to give the device an overclock!(Make sure to provide enough cooling to your pi otherwise you might break it.)

```bash
over_voltage=4
arm_freq=2000
gpu_freq=650
```

4. Insert your sd-card and boot from it. Find the ip of your raspberry pi. You can find it in your router admin page or use an app like fing on your mobile phone(Your raspberry pi and phone need to be in the same network) You can acces your server now with ssh through an terminal. (The initial password is ubuntu and during the first time you login you will be asked to change it.)

You can use the following command to login into your raspberry pi through ssh with the following command (Replace the ip adress with the ip adress of your raspberry pi.)

```bash
ssh ubuntu@192.1.1.11
```
### Install log2ram

Follow the installation instructions on [log2ram github page](https://github.com/azlux/log2ram)

Edit the log2ram configuration file to use more ram. (at least 100MB)
### Add an external USB harddisk.

To add an external usb drive u need to partition it and mount it.
the necessary steps are described in the following article.

- [How To Partition and Format Storage Devices in Linux](https://github.com/jhhest/docker-server/blob/master/How%20To%20Partition%20and%20Format%20Storage%20Devices%20in%20Linux.md)

### 

### install and configure samba file sharing.

> I use samba for easy file sharing across my network. I used the following ubuntu tutorial to install samba [Install and Configure Samba](https://ubuntu.com/tutorials/install-and-configure-samba#1-overview)

To install Samba, we run:

```bash
sudo apt update
sudo apt install samba
```

We can check if the installation was successful by running:

```bash
whereis samba
```

The following should be its output:

```bash
samba: /usr/sbin/samba /usr/lib/samba /etc/samba /usr/share/samba /usr/share/man/man7/samba.7.gz /usr/share/man/man8/samba.8.gz
```

#### Setting up Samba

Now that Samba is installed, we need to create a directory for it to share:

```bash
mkdir /home/<username>/sambashare/
```

The command above creates a new folder `sambashare` in our home directory which we will share later.

The configuration file for Samba is located at `/etc/samba/smb.conf`. To add the new directory as a share, we edit the file by running:

```bash
sudo nano /etc/samba/smb.conf
```

At the bottom of the file, add the following lines:

```text
    [sambashare]
        comment = Samba on Ubuntu
        path = /home/username/sambashare
        read only = no
        browsable = yes
```

Then press `Ctrl-O` to save and `Ctrl-X` to exit from the _nano_ text editor.

##### What we've just added

- comment: A brief description of the share.

- path: The directory of our share.
- read only: Permission to modify the contents of the share folder is only granted when the value of this directive is `no`.
- browsable: When set to `yes`, file managers such as Ubuntu's default file manager will list this share under "Network" (it could also appear as browseable).

Now that we have our new share configured, save it and restart Samba for it to take effect:

```bash
sudo service smbd restart
```

Update the firewall rules to allow Samba traffic:

```bash
sudo ufw allow samba
```

#### Setting up User Accounts and Connecting to Share

Since Samba doesn't use the system account password, we need to set up a Samba password for our user account:

```bash
sudo smbpasswd -a username
```

> ⓘ Username used must belong to a system account, else it won't save.

### Connecting to Share

On Ubuntu: Open up the default file manager and click _Connect to Server_ then enter:
![ubuntuctn](https://ubuntucommunity.s3.dualstack.us-east-2.amazonaws.com/original/2X/7/7e0831db9f6dc65fa530832163f6e865d746ea32.png)

On macOS: In the Finder menu, click _Go \> Connect to Server_ then enter:
![macosctn](https://ubuntucommunity.s3.dualstack.us-east-2.amazonaws.com/original/2X/0/027b1f7d34951cada9c002c3250c1aff148ccc7b.png)

On Windows, open up File Manager and edit the file path to:

```address
\\ip-address\sambashare
```

Note: `ip-address` is the Samba server IP address and `sambashare` is the name of the share.

You'll be prompted for your credentials. Enter them to connect! 
![Samba](https://ubuntucommunity.s3.dualstack.us-east-2.amazonaws.com/original/2X/5/56bda4786ea6393efec317d90cf413796503e1d7.png)

### If you'd like to take your configuration further...

- [Samba Server Guide](https://help.ubuntu.com/community/Samba/SambaServerGuide)

### Install docker and composer.

Steps

1. Install Docker

```bash
curl -sSL https://get.docker.com | sh
```

2. Add permission to ubuntu User to run Docker Commands

```bash
sudo usermod -aG docker ubuntu
```

Reboot here or run the next commands with a sudo

3. Test Docker installation

```bash
docker run hello-world
```

4. IMPORTANT! Install proper dependencies

```bash
sudo apt-get install -y libffi-dev libssl-dev

sudo apt-get install -y python3 python3-pip

sudo apt-get remove python-configparser
```

5. Install Docker Compose

```bash
sudo pip3 install docker-compose
```

6. Add Linux User to Docker Group
   Running and managing docker containers requires sudo privileges. So this means you will have to type sudo for every command or switch to the root user account. But you can get around this by adding the current user to the docker group using the following command:

```bash
sudo usermod -aG docker ${USER}
```

While this can be a minor security risk, the chances are very less and this is not an enterprise level setup but a home setup. You can do this for convenience.

### environment variables

Each container has it own environmental variables such as timezone, user id, user group, etc. that docker containers should use. In this case a lot of these variables will be the same for these containers. Create / edit the environmental variables file using the following command:

```bash
sudo nano /etc/environment
```

Add the following as separate lines at the end of the file:

```bash
PUID=xxxx
PGID=xxx
TZ="Europe/Amsterdam"
USERDIR="/home/xxxx"
MYSQL_ROOT_PASSWORD="superSecretPassword"
```

Ofcourse you have to adjust these values (especially values like `xxxx`) to your own situation and hardware. `PUID` and `PGID` can be retrieved by executing the command `id` in your terminal.

Find out your timezone at the [Timezone Database](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones)

Find out your `USERDIR` by executing `cd ~ ; pwd` in your terminal.

## Docker compose

> There are several way's of setting up containers with docker. Docker compose is one way of setting up containers. You can view my docker-compose file as an example. 

The volumes you map from a container to a folder outside of the container must have the right user rights. 

In example for node red to work to folder must have read and write permissions from the normal user. 

sudo chown -R 1000:1000 path/to/your/node-red/data

### todo
- https://www.raspberrypi.org/forums/viewtopic.php?t=237735


## Sources

I used the following sources to setup my raspberry pi server.

### Video tutorials

- [Andreas Spiess: #295 Raspberry Pi Server based on Docker, with VPN, Dropbox backup, Influx, Grafana, etc.](https://www.youtube.com/watch?v=a6mjt8tWUws)
- [ETA PRIME: Install Ubuntu 19.10 On The Raspberry Pi 4 Full Ubuntu Desktop](https://www.youtube.com/watch?v=HMo9C7LCzE0&t=35s)
- [Install Docker and Docker Compose on Raspberry pi 4(Raspbian Buster)](https://medium.com/@techiebouncer/install-docker-and-docker-compose-on-raspberry-pi-4-raspbian-buster-c5b78b9a0d08)

### articles

- [Ultimate Smart Home Media Server with Docker and Ubuntu 18.04 – Basic](https://www.smarthomebeginner.com/docker-home-media-server-2018-basic/#What_is_a_Home_Media_Server)

- [Traefik Tutorial: Traefik Reverse Proxy with LetsEncrypt for Docker Media Server](https://www.smarthomebeginner.com/traefik-reverse-proxy-tutorial-for-docker/)

- [Official ubuntu server guide](https://help.ubuntu.com/lts/serverguide/index.html)

- [Ubuntu documentation: User Management](https://help.ubuntu.com/lts/serverguide/user-management.html)

- [Linux file permissions](https://www.pluralsight.com/blog/it-ops/linux-file-permissions)

- [An Introduction to Storage Terminology and Concepts in Linux](https://www.digitalocean.com/community/tutorials/an-introduction-to-storage-terminology-and-concepts-in-linux)

- [How To Partition and Format Storage Devices in Linux](https://www.digitalocean.com/community/tutorials/how-to-partition-and-format-storage-devices-in-linux)

- [Ubuntu documentation: Installing a new harddrive](https://help.ubuntu.com/community/InstallingANewHardDrive)

- [Partitioning, Formatting, and Mounting a Hard Drive in Linux Ubuntu 18.04 (2020)](https://medium.com/@sh.tsang/partitioning-formatting-and-mounting-a-hard-drive-in-linux-ubuntu-18-04-324b7634d1e0)

- [Compatible USB // Raspberry Pi 4 USB Boot Config Guide for SSD / Flash Drives](https://jamesachambers.com/raspberry-pi-4-usb-boot-config-guide-for-ssd-flash-drives/)
