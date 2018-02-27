---
layout: post
comments: true
title: "Editing Installing Arch Linux on a Macbook Pro Retina"
date: 2016-03-26
categories: linux configs
---

_This install guide is a copy of [this blog post](https://vec.io/posts/use-arch-linux-and-xmonad-on-macbook-pro-with-retina-display)
with my own notes/thoughts along the way. The purpose of this is primarily to remind me of how I
installed arch on my Macbook Pro Retina (Mid 2014)._

## Prepare

__WARNING: PLEASE MAKE SURE YOU KNOW EXACTLY WHAT EACH COMMAND DOES, ESPECIALLY THE DISK RELATED, THIS MAY ERASE YOUR IMPORTANT DATA PERMANENTLY! NO WARRANTY!__

Update the Mac OSX and reboot, then run the Disk Utitlity to resize the Macintosh HD to 100GB, all the remaining space are reserved for Linux.

Prepare an Arch Linux USB disk:
- Download the iso [https://www.archlinux.org/download/](https://www.archlinux.org/download/)
- Copy it to your usb drive using dd `dd bs=512k if=archlinux.iso of=/dev/sdb` You can find your usb
  device (/dev/sdb) using `diskutil list`.

## Partitions

Reboot from USB by keep pressing the option key, and you will be prompted the zsh console from
Arch Linux installer.

Run `cgdisk /dev/sda` to create partitions according to the following chart:

<center>
<img src="/assets/posts/partitions.png">
</center>

## Network

The installation of Arch Linux needs an active network connection, I've three simple choices to connect Internet.

- The USB Tether feature from my Android devices and iPhone 5. It just works.
- A Thunderbolt to gigabit Ethernet adapter, connect it before booting. Thunderbolt hotplugging is not supported.
  - You might need to run `dhcpcd ens9`. Use `ip link` or `iw dev` to find your ethernet device.
- Use wireless network, it needs a working network to install the broadcom-wl-dkms. Then run wifi-menu to connect to WIFI. It works with great performance!
   There is more on this in configuration section of the install guide.


## Install

At first, encrypt my home partition with dm-crypt:
NOTE: If you don't wish to encrypt your home partition, skip this step.
```bash
cryptsetup -c aes-xts-plain64 -y -s 512 luksFormat /dev/sda7
cryptsetup luksOpen /dev/sda7 home
```

Format partitions and install the system.
```bash
mkfs.ext2 /dev/sda5
mkfs.ext4 /dev/sda6
mkfs.ext4 /dev/mapper/home # If your home partition is encrypted
mkfs.ext4 /dev/sda7        # If your home partition is not encrypted

mount /dev/sda6 /mnt
mkdir /mnt/boot && mount /dev/sda5 /mnt/boot
mkdir /mnt/home && mount /dev/mapper/home /mnt/home # Home partition encrypted
mkdir /mnt/home && mount /dev/sda7 /mnt/home        # Home partition not encrypted

pacstrap /mnt base base-devel
genfstab -p /mnt >> /mnt/etc/fstab

echo 'home  /dev/sda7' >> /mnt/etc/crypttab         # Home partition encrypted
```

As the storage device is an SSD, so I tuned some parameters in `/mnt/etc/fstab`:
```bash
/dev/sda5        /boot  ext2  defaults,relatime,stripe=4    0 2
/dev/sda6        /      ext4  defaults,noatime,discard,data=writeback   0 1

# If your home drive is encrypted
/dev/mapper/home /home  ext4  defaults,noatime,discard,data=ordered 0 2

# If it is not
/dev/sda7 /home  ext4  defaults,noatime,discard,data=ordered 0 2
```

## Configure

After everything is installed, do some basic configuration.

```bash
arch-chroot /mnt /bin/bash
echo vecio > /etc/hostname # Replace vecio with your desired hostname
hwclock --systohc --utc

# Create username and password. Replace 'cedric' with your username.
# You will be prompted for a password
useradd -m -g users -G wheel -s /bin/bash cedric && passwd cedric

pacman -S sudo

# Uncomment this line to allow access to sudo:
# %wheel      ALL=(ALL) ALL
nano /etc/sudoers
```

Set your local time. Replace `Asia/Hong_Kong` with your `Zone/Region`. You can view zones
with `ls /usr/share/zoneinfo/` and regions with `ls /usr/share/zoneinfo/Zone/`.
```bash
ln -s /usr/share/zoneinfo/Asia/Hong_Kong /etc/localtime
```

To set locale, edit `/etc/local.gen` and uncomment the locales you want.
```bash
# Example
en_US.UTF-8 UTF-8
```

Then generate the locales and set the LANG environment variable:
```bash
locale-gen
echo LANG=en_US.UTF-8 > /etc/locale.conf
export LANG=en_US.UTF-8
```

Also modify `/etc/mkinitcpio.conf` to insert `keyboard` after `autodetect`
in the hooks section.
```bash
# Then
mkinitcpio -p linux
```

## Bootloader

I want to boot directly from MacBook's EFI boot loader, so I just need to craft the boot.efi:
```bash
pacman -S grub
```

At this point, remember to alter /etc/default/grub with:
```bash
GRUB_CMDLINE_LINUX_DEFAULT="quiet rootflags=data=writeback"
```

Now generate the boot.efi and place it somewhere such as a USB device:
```bash
# Create the boot.efi file. It will be placed in the current directory.
grub-mkconfig -o boot/grub/grub.cfg
grub-mkstandalone -o boot.efi -d usr/lib/grub/x86_64-efi -O x86_64-efi --compress=xz boot/grub/grub.cfg

mkdir -p /mnt/usb       # Create usb mount point
mount /dev/sdb /mnt/usb # Mount the usb device
cp boot.efi /mnt/usb    # Copy boot.efi to usb
umount /mnt/usb
```

Then exit chroot and umount all filesystems and reboot to Mac OSX again. Launch
Disk Utility to format (e.g. erase) `/dev/sda4` to HFS+ (Mac OS Extended Journaled),
it's the place where grub2's image will live in.

According to this article, create directories and files in `/dev/sda4` with the
following structures:
```
|-- System
|   `-- Library
|       `-- CoreServices
|           |-- SystemVersion.plist
|           `-- boot.efi
`-- mach_kernel
```

The `mach_kernel` is a blank directory and `boot.efi` is generated by `grub-mkstandalone`.
The contents of `SystemVersion.plist`:
```
<xml version="1.0" encoding="utf-8"?>
<plist version="1.0">
<dict>
    <key>ProductBuildVersion</key>
    <string></string>
    <key>ProductName</key>
    <string>Linux</string>
    <key>ProductVersion</key>
    <string>Arch Linux</string>
</dict>
</plist>
```

After all files created, we need to `bless` this partition to make it bootable by issuing
NOTE: To do this on OS X El Capitan and later, you will need to temporarily disable
System Integrity Protection.

__Disable SIP__ (Skip this step if you are still on Yosemite or below)

1. Restart the computer, while booting hold down Command-R to boot into recovery mode.
1. Once booted, navigate to the “Utilities > Terminal” in the top menu bar.
1. Enter `csrutil disable` in the terminal window and hit the return key.
1. Restart the machine and System Integrity Protection will now be disabled.

After disabled, bless the partition
```
sudo bless --device disk0s4 --setBoot
```

__Reenable SIP__

Same Steps as disabling but enter `csrutil enable`.

Your Arch Linux partition might be labeled `EFI Boot`. [There seems
to be a way to change this](http://apple.stackexchange.com/questions/188149/new-efi-boot-drive-appearing-after-reverting-os-x-drive-to-hfs),
but I haven't been able to get it to work.

You also might want to select which disk is the default startup disk. I think during this installation,
Arch is made the default. You can specify which one you want by [following these steps](https://support.apple.com/en-us/HT204417)

Then Reboot to Arch Linux.

## WM or DE

Install your preferred window manager or desktop environment. If you choose to use a window manager,
you can start it by adding the command to the end of your `~/.xinitrc`.

### XMonad
```bash
yaourt -S xmonad xmonad-contrib cabal-install slim terminator \
         xmobar dmenu-xft trayer scrot xscreensaver feh parcellite \
         pidgin networkmanager-applet xfce4-notifyd gnome-keyring \
         git nautilus ranger
cabal update && cabal install yeganesh
systemctl enable NetworkManager
systemctl enable slim
systemctl disable bluetooth # Because I never use it
```
Then I cloned vicfryzel's xmonad config from https://github.com/vicfryzel/xmonad-config,
and made several small tweaks.

Add the following lines to `~/.xinitrc` to manage all the keys of the session,
e.g. SSH key passphrase, Wi-Fi password.

```bash
# Start a D-Bus session
source /etc/X11/xinit/xinitrc.d/30-dbus
# Start GNOME Keyring
eval $(/usr/bin/gnome-keyring-daemon --start --components=gpg,pkcs11,secrets,ssh)
# You probably need to do this too:
export SSH_AUTH_SOCK
export GPG_AGENT_INFO
export GNOME_KEYRING_CONTROL
export GNOME_KEYRING_PID
```
Also remember to remove the deperacted ck-launch-session from ~/.xinitrc,
change to exec dbus-launch --sh-syntax --exit-with-session xmonad,
this will fix the Trash and Network related problems in nautilus:

### i3wm

```bash
yaourt -S i3
```

Add `exec i3` to the end of your `~/.xinitrc`

type `startx` to begin


## Apperance

I have to admit it's hard to find the theme and icons to work properly on a Retina screen.
At last, I found Numix as the GTK theme, KDE's oxygen as icons, and Vanilla DMZ as the
cursor theme.

To change GTK's theme, you can edit `~/.gtkrc-2.0` and `~/.config/gtk-3.0/settings.ini`,
or simply use `lxapperance`.

In Firefox's `about:config`, set `layout.css.devPixelsPerPx to 2`, then everything in the
web will be retina ready, also recommend to install the FXChrome theme for Firefox.

If you'd prefer to use Google Chrome, install it with `yaourt google-chrome`. In order for
it to look ok on retina screens, you might need to start it with this command:
```bash
google-chrome --force-device-scale-factor=2
```

Finally, the wallpaper is achieved by `feh --bg-fill -z ~/Pictures/Wallpapers`
