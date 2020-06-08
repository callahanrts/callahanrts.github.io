---
layout: post
comments: false
title: "Archlinux Package Install Notes"
date: 2016-08-14
categories: linux configs
---

Notes on installing various Archlinux packages. This way I won't forget what I
did if something breaks and I have to reinstall something.

# Configuration
If ethernet is not working, do `ip a` to see listed network devices. Mine shows `ens9` as my
thunderbolt ethernet. To start ethernet run `dhcpcd ens9`. Test if it worked by `ping -c3 www.google.com`

sources:
  - https://bbs.archlinux.org/viewtopic.php?id=156427
  - https://www.cyberciti.biz/faq/linux-list-network-cards-command/

[Install the `yaourt` package manager](https://revryl.com/2013/07/11/yaourt-installation-arch-linux/)

# OS Components

### gnome-settings-daemon

I'm not entirely sure what all this is doing. I need to find some docs and read more about what
it adds. It does allow the Emacs Gtk+ theme to work so I can use ctrl-w and ctrl-h in the browser
without closing tabs and opening history. Might be overkill for that single use, so I need to
figure out what else it's doing and see if I want those things.

```
yaourt gnome-settings-daemon
```

### Notifications Daemon
[dunst](https://github.com/knopwob/dunst)
```
yaourt dunst-git
```

### WiFi
```bash
# Packages
sudo pacman -S wireless_tools

# Install the package
yaourt broadcom-wl

# Load the kernel module
sudo modprobe wl
sudo depmod -a
```

### Fonts

https://www.techrapid.co.uk/linux/arch-linux/improve-font-rendering-on-arch-linux/

```
yaourt ttf-mac-fonts
```

### Touchpad

https://wiki.archlinux.org/index.php/MacBookPro11,x#Touchpad
https://medium.com/@philpl/arch-linux-running-on-my-macbook-2ea525ebefe3#.u5cdphtsp

```bash
yaourt -S xf86-input-mtrack-git
sudo vim /etc/X11/xorg.conf.d/60-mtrack.conf
```

```
# /etc/X11/xorg.conf.d/60-mtrack.conf
Section "InputClass"
    MatchIsTouchpad "on"
    Identifier      "Touchpads"
    Driver          "mtrack"
    Option          "Sensitivity" "0.64"
    Option          "FingerHigh" "5"
    Option          "FingerLow" "2"
    Option          "IgnoreThumb" "true"
    Option          "IgnorePalm" "true"
    Option          "DisableOnPalm" "true"
    Option          "TapButton1" "1"
    Option          "TapButton2" "3"
    Option          "TapButton3" "3"
    Option          "TapButton4" "0"
    Option          "ClickFinger1" "1"
    Option          "ClickFinger2" "2"
    Option          "ClickFinger3" "2"
    Option          "ButtonMoveEmulate" "false"
    Option          "ButtonIntegrated" "true"
#    Option          "ClickTime" "25"
    Option          "BottomEdge" "30"
    Option          "SwipeLeftButton" "8"
    Option          "SwipeRightButton" "9"
    Option          "SwipeUpButton" "0"
    Option          "SwipeDownButton" "0"
#    Option          "ScrollDistance" "75"
    Option          "VertScrollDelta" "-90"
    Option          "HorizScrollDelta" "-90"

    ## Natural Scrolling
    Option          "ScrollUpButton" "5"
    Option          "ScrollDownButton" "4"
    Option          "ScrollLeftButton" "7"
    Option          "ScrollRightButton" "6"
EndSection
```

```
# Add yourself to the users group
sudo gpasswd -a tuxinator input

# Use natural scrolling
echo "pointer = 1 2 3 5 4 6 7 8 9 10 11 12" >> ~/.Xmodmap
```

### Power Settings

[Powertop](https://wiki.archlinux.org/index.php/Powertop) for watching power usage of various components. It has some tuning features, but I'm currently not using them.
```
sudo pacman -S powertop
```

[TLP](https://wiki.archlinux.org/index.php/TLP) for system wide power management. TLP config is stored in `/etc/default/tlp`.

Use [CPU Frequency Scaling](https://wiki.archlinux.org/index.php/CPU_frequency_scaling) (cpupower) to slow down processors when the macbook is getting to warm. Use and configure [mbpfan-git](https://github.com/dgraziotin/mbpfan) to fine tune the Macbook Pro fans and keep CPU temps reasonable.

Arch wiki power management resources:
  - General: [Power Management](https://wiki.archlinux.org/index.php/Power_management)
  - Laptop Specific: [Laptop](https://wiki.archlinux.org/index.php/laptop#Power_management)
  - Macbook Specific: [Macbook 11,x#Powersave](https://wiki.archlinux.org/index.php/MacBookPro11,x#Powersave)

### Keyboard Backlight

https://wiki.archlinux.org/index.php/MacBook#Keyboard_Backlight

### Backlight

https://wiki.archlinux.org/index.php/MacBookPro11,x#Screen_backlight

### File Managers

Nautilus (Gnome gui file manager)
```
sudo pacman -S nautilus
```

Ranger (Command line file manager)
```
sudo pacman -S ranger
```

### Linux Monitoring Sensors

```
sudo pacman -S lm_sensors

# View temperatures
sensors
```

# Appearance

### lxappearance
Manage gtk themes
```
sudo pacman -S lxappearance
```

### Lemonbar
Slick status bar
```
yaourt lemonbar-git

# Battery stats
sudo pacman -S acpi
```


# Applications

### Zsh
```
sudo pacman -S zsh # Install zsh shell
sudo chsh -s $(which zsh)
```
[Oh my zsh](https://github.com/robbyrussell/oh-my-zsh)

### NVM

https://github.com/creationix/nvm

### Element

https://github.com/callahanrts/element

### Ag

```bash
sudo pacman -S silver-searcher-git
```

### i3
```
sudo pacman -S dmenu rofi xdotool alsa-utils xbindkeys gnome-terminal conky feh cmake htop upower

# Backlight
yaourt light-git compton-git
```

### Rofi

Rofi config is in Xresources

### Redis
yaourt -S redis

### Docker

https://docs.docker.com/engine/installation/linux/archlinux/

At work, many people use mac with docker. For docker to run on a mac, it needs to run a vm. In our
case, the vm has an IP of 192.168.99.100. Since our configs, namely database.yml, have this
hardcoded into them, we need to find a way to reroute this ip to localhost on linux.

Get ifconfig if it doesn't exist already:
```
sudo pacman -S net-tools
```

https://superuser.com/questions/572077/redirect-some-ip-to-localhost
```
# Add this to your startup script:
sudo ifconfig lo:1 inet 192.168.99.100 netmask 255.255.255.255 up
```

```
# Check if it's working
ping -c3 192.168.99.100

# Output
#
# PING 192.168.99.100 (192.168.99.100) 56(84) bytes of data.
# 64 bytes from 192.168.99.100: icmp_seq=1 ttl=64 time=0.031 ms
# 64 bytes from 192.168.99.100: icmp_seq=2 ttl=64 time=0.028 ms
# 64 bytes from 192.168.99.100: icmp_seq=3 ttl=64 time=0.029 ms
#
# --- 192.168.99.100 ping statistics ---
# 3 packets transmitted, 3 received, 0% packet loss, time 1998ms
# rtt min/avg/max/mdev = 0.028/0.029/0.031/0.004 ms
```


### MySQL
https://wiki.archlinux.org/index.php/MySQL

```
# Install mariadb
sudo pacman -S mariadb
sudo mysql_install_db --user=mysql --basedir=/usr --datadir=/var/lib/mysql

# Enable the service to start at runtime and start it now.
sudo systemctl enable mysqld
sudo systemctl start mysqld
```
Sequel Pro like gui for managing mysql databases on linux
[MySQL Workbench](https://www.mysql.com/products/workbench/)
```
sudo pacman -S mysql-workbench
```


### Spotify

https://wiki.archlinux.org/index.php/spotify

```
# Spotify Beta seems to work best with hidpi for me
yaourt -S spotify-beta

# HiDPI mode
spotify --force-device-scale-factor=1.8
```


### RVM

Had to do the open_ssl fix at the bottom here. Some were also saying there was a readline issue.
Try doing the `rvm pkg install readline` and install with the `--with-readline-dir=$HOME/.rvm/usr`
as well as `--with-openssl-dir=$HOME/.rvm/usr`.
https://wiki.archlinux.org/index.php/RVM#Installing_RVM

### tmux

```
sudo pacman -S tmux
```

### Assorted packages

```
sudo pacman -S imagemagick # image manipulation
```

### Last Pass

```
yaourt lastpass-cli
```

### VMWare Workstation
[Install](https://wiki.archlinux.org/index.php/VMware#Kernel_modules_fail_to_build_after_Linux_4.6)
[If it doesn't start](https://bbs.archlinux.org/viewtopic.php?id=209993)
