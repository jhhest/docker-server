# Raspberry Pi Docker-server

This document is my personal log of making a home server with a raspberry pi 4 (4GB model).
![raspberry pi](IMG/a999bb5f-Raspberry+Pi+4.webp)

I document my steps to learn how to work with docker, docker-compose and ubuntu.

## installation

### Install ubuntu

1. [Download](https://ubuntu.com/download/raspberry-pi) the newest ubuntu raspberry pi image. (Use an image that is newer than ubuntu 19.10. Older versions don't support 4GB raspberry pi model)

2. Write an image to an sd-card. I use the disks application in ubuntu to write an image to an sd-card. When i open up the disks application an select the sd-card on the left pane. On the left of the minimize button you wille a 3 dotted button, select this to see the drive options menu. In the drive option menu select the "restore disk image option." Select the image you downloaded from step 1 to flash the sd card. If you would like to know more about flashing an sd card with an operatingsystem you can look at the [OMV installationguide](https://github.com/OpenMediaVault-Plugin-Developers/docs/blob/master/Adden-B-Installing_OMV5_on_an%20R-PI.pdf). They have an extensive document about installing omv and the first chapter they explain a lot about how to test the integrity of you sd-card with and how to make sure the disk is going to work properly on your system. If you don't like reading you can do a quick setup with this video from [ETA-prime](https://www.youtube.com/watch?v=HMo9C7LCzE0)

3. Before you put you sd-card in your raspberry pi to boot ubuntu you first have to edit some configuration files. Go to disks application and mount the "boot" partition. create an empty file without any extension and name it "SSH". This will enable ssh on boot, so you are able to use ssh. I will use a ethernet cable to attach my raspberry pi to my home network. In this partition you will find a readme with information about config.txt. A raspberry pi doesn't come with a nice graphical interface. You need to edit config.txt to make changes in the hardware interface of the device. Use the following lines to give the device an overclock!(Make sure to provide enough cooling to your pi otherwise you might break it.)

```bash
over_voltage=4
arm_freq=2000
gpu_freq=650
```

### todo

## Sources

I used the following sources to setup my raspberry pi server.

https://medium.com/@techiebouncer/install-docker-and-docker-compose-on-raspberry-pi-4-raspbian-buster-c5b78b9a0d08
