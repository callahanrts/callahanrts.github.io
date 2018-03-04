---
layout: post
comments: false
title: "Notes on Upgrading Archlinux on a Macbook Pro"
date: 2016-08-06
categories: linux configs
---


Often, when Arch is upgraded, things will be broken. This is a list of ways problems have been
solved in the past. Hopefully it will be helpful for solving issues when upgrading in the future.

## System
### Boot Problems
Once in a while there will be no keyboard input at the login prompt. The cause/fix is currently unknown, but [boot debugging](https://wiki.archlinux.org/index.php/boot_debugging) might help to find a fix.

### WiFi
If wifi does not work after upgrading, try reinstalling `broadcom-wl-dkms`
```
# Remove current installation
sudo pacman -R broadcom-wl-dkms

# Install the package again
yaourt broadcom-wl-dkms

# Reload the kernel module
sudo modprobe wl

# Double check that you can see available wifi connections
wifi-menu
```

## Applications
### VMWare Workstation
__Post Install__
http://askubuntu.com/questions/689123/vmware-wont-work-after-ubuntu-upgrade

The solution below applies to versions of VMWare prior to 12.1.0. The 12.1.0 version of VMWare does not require this fix.

VMWare and VMPlayer are in fact looking for a specific library string. You can execute VMWare/VMPlayer from the terminal by executing

```bash
export LD_LIBRARY_PATH=/usr/lib/vmware/lib/libglibmm-2.4.so.1/:$LD_LIBRARY_PATH
```

then `vmware` or `vmplayer`

I made this a permanent change on my system by executing instead

executing `sudo nano /usr/bin/vmware` and adding the line

```bash
export LD_LIBRARY_PATH=/usr/lib/vmware/lib/libglibmm-2.4.so.1
```

after the line `export PRODUCT_NAME...`

Press ctrl+o to save and ctrl+x to exit: VMWare will now work.

To perform the same function for VMPlayer, execute the same changes to `/usr/bin/vmplayer`
