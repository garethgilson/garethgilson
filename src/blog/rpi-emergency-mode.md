---
layout: layout.njk
title: RPI Emergency Mode FSTab
tags:
    - blog
    - technical
    - rpi
date: 2022-01-03
---
# Fixing Raspberry Pi in Emergency Mode due to FSTab Misconfiguration

1. Remove the SD Card from the RPI and put into another computer
2. Open `cmdline.txt` in Notepad and add `init=/bin/sh` to the end of the line (not on a new line)
3. Save, move the SD card back to the RPI and boot, it should boot to a root console
4. Enter the command `mount -o remount,rw / --target /` to make the filesystem writeable
5. Make the needed changes to fstab with `nano /etc/fstab` (no need for `sudo`, you are root right now)
6. Press `Ctrl-X` to exit, and `Y` to write changes, then type `exit` at the command line, once you get to "End Kernel Panic" pull the plug on the RPI
7. Put the SD card back in the alternate computer, and remove `init=/bin/sh` from the `cmdline.txt` file
8. Replace the SD card and reboot the RPI
