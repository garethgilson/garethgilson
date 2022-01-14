---
layout: layout.njk
title: RPI File and Media Server
tags:
    - blog
    - technical
    - rpi
date: 2022-01-02
---

# Configuring a Raspberry Pi as a File and Media Server

**Goal:** Set up a Raspberry Pi with my external hard drives and use Samba and Emby to have it act as a file and media home server to be access from our family's various computers and (so-called) smart devices.

**N.B.**: I am intentionally not setting up any wireless networking because since I'm using this for file and media sharing, I will only be using a hard-wired ethernet connection.

**N.B.**: I'm also using my router to set a static internal IP address for the RPI - refer to your router's documentation for instruction on how to do that.

## Supplies and Equipment Used
* RPI 3 Model B
* 16GB microSD Card
* 1+ External HDD/SSD
* MicroUSB Power Cable
* Ethernet

## Install and Update
1. Insert the microSD card into a regular computer and use Raspberry Pi Imager to install Raspbian Lite
2. Insert the microSD card into the RPI and connect everything (except the external storage) to start configuration
    * At this point I have a keyboard and monitor plugged into the RPI for the initial configuration
3. Login using the default username **pi** and the default password **raspberry**
4. Run `sudo apt update` and `sudo apt upgrade` to bring the default software up-to-date
5. Run `sudo raspi-config` to configure RPI specific options:
    * System Options > Password: Change the default password
    * Interface Options > SSH: Enable
    * Performance Options > GPU Memory: Set to 16 (headless and CLI only means no video memory needed, keep it for disk ops)
    * Localisation Options > Timezone: Set to your local timezone
    * Advanced Options > Expand Filesystem: To ensure that all of the SD card is usable for the OS

At this point you can optionally disconnect the external monitor and keyboard and connect to the RPI via SSH from another computer.

## Mount the External Drive(s)
1. Plug in all the external drive, taking note of the size of each drive.
2. Run `sudo fdisk -l` to get a list of the drives plugged in, and take note of the device name
    * First one will likely be `/dev/sda`, second one `/dev/sdb` and so on; cross-reference using the size of the drive you're expecting
3. Make a mount point directory, for example, for a drive for projects, run `sudo mkdir /media/projects-hdd`
4. Mount the drive to the point by running `sudo mount /dev/sda1 /media/projects-hdd`
    * Change `/dev/sda1` to match the listing from `sudo fdisk -l`, the number at the end may change if you have more than one partition on the drive to mount
5. Make it mount automatically on boot by:
    1. Running `sudo nano /etc/fstab`
    2. Add a new line at the bottom of the file with the following information, using a tab to separate each column
        1. `/dev/sda1/` or the device name from Step 2
        2. `/media/projects-hdd` or the mount point from Step 3
        3. `ext4` or the filesystem if something else (try to avoid using something other than ext3 or ext4 for simplicity)
        4. `defaults` to use the default mount settings
        5. `0` to not use the drive as a backup drive
        6. `0` to not have `fsck` check the disk on mount
    3. Save by typing `Ctrl-X` and `Y` then `Return` to write the changes
6. Repeat steps 3 to 5 as needed for each drive.
7. Reboot using `sudo shutdown -r now` and make sure the drives mounted successfully
    * If the RPI reboots into emergency mode, you probably pooched the fstab entry, see [Fixing Raspberry Pi in Emergency Mode](/blog/rpi-emergency-mode)

## Set up Samba
1. Install Samba by running `sudo apt install samba samba-common-bin`
2. For each drive or partition you want to share on the network, add an entry to `smb.conf`
    1. Run `sudo nano /etc/samba/smb.conf`
    2. Add entries along the lines of:
        <pre>
        [projects]
            comment = current and closed projects
            path = /media/projects-hdd
            create mask = 0777
            directory mask = 0777
            read only = no
            guest ok = yes
            writable = yes
            guest account = pi
            browsable = yes
        </pre>
    3. Close and write changes and run `sudo service smbd restart` to have them take effect
    4. The shares should now be accessible from networked computers

## Set up Emby
1. Get the link for the **armhf** version of the Emby server from [the website](https://emby.media/linux-server.html)
2. Use `wget https://github.com/MediaBrowser/Emby.Releases/releases/download/4.6.7.0/emby-server-deb_4.6.7.0_armhf.deb` to download the package
3. Run `sudo dpkg -i emby-server-deb_4.6.7.0_armhf.deb` to install Emby
4. Go to `http://HOSTNAME:8096` to configure the server using their setup wizard
