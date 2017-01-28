---
layout: post
title: "Installing Windows 10 in a Virtual Machine"
description: "Steps to install Windows 10 as a virtual machine using VirtualBox."
tags: []
---

How to install windows 10 in a virtual machine.

# Dependencies
### VirtualBox
[Install VirtualBox](https://www.virtualbox.org/wiki/Downloads)

### Windows 10
###### Go [here](https://www.microsoft.com/en-us/software-download/windows10ISO)
###### Select `windows 10`, and `English`

{% include image.html path="posts/win10/win10-1.png" path-detail="posts/win10/win10-1.png" alt="Chalk intro" %}

###### If you have < 8GB of RAM, download 32-bit, otherwise download 64-bit

{% include image.html path="posts/win10/win10-2.png" path-detail="posts/win10/win10-2.png" alt="" %}

# Installation

###### Open virutalbox
###### Select New

{% include image.html path="posts/win10/vb-1.png" path-detail="posts/win10/vb-1.png" alt="" %}

###### Label the new Virtual Machine as Windows10 (Be sure to select 64/32 bit--whichever one you downloaded)
###### Give your virtual machine half the available ram on your computer. If you have 4GB then use 2048, if you have 8GB then 4096, etc.

{% include image.html path="posts/win10/vb-3.png" path-detail="posts/win10/vb-3.png" alt="" %}

###### Click create

{% include image.html path="posts/win10/vb-4.png" path-detail="posts/win10/vb-4.png" alt="" %}

###### Continue

{% include image.html path="posts/win10/vb-5.png" path-detail="posts/win10/vb-5.png" alt="" %}

###### Continue

{% include image.html path="posts/win10/vb-6.png" path-detail="posts/win10/vb-6.png" alt="" %}

###### Select an amount >32GB. It won't actually take up this amount of space. The total size, installed, is about 23GB. You'll need a little more room for applications, though.

{% include image.html path="posts/win10/vb-7.png" path-detail="posts/win10/vb-7.png" alt="" %}

###### Click Start to power up your Virtual Machine

{% include image.html path="posts/win10/vb-8.png" path-detail="posts/win10/vb-8.png" alt="" %}

###### Click the folder with the green arrow
###### Select the Windows 10 iso you downloaded
###### Click Start

{% include image.html path="posts/win10/vb-9.png" path-detail="posts/win10/vb-9.png" alt="" %}

###### Click Next

{% include image.html path="posts/win10/vb-10.png" path-detail="posts/win10/vb-10.png" alt="" %}

###### Click Install

{% include image.html path="posts/win10/vb-11.png" path-detail="posts/win10/vb-11.png" alt="" %}

###### Click `I don't have a product key`

{% include image.html path="posts/win10/vb-12.png" path-detail="posts/win10/vb-12.png" alt="" %}

###### Select `Windows 10 Home` and click next

{% include image.html path="posts/win10/vb-13.png" path-detail="posts/win10/vb-13.png" alt="" %}

###### Accept the license
###### Continue
###### Select Custom
###### Click Next

{% include image.html path="posts/win10/vb-14.png" path-detail="posts/win10/vb-14.png" alt="" %}

## Fix screen resolution issue
The resolution isn't taking up the full size of the window. Let's fix that.

###### Click Devices > Insert guest additions CD Image

{% include image.html path="posts/win10/vb-15.png" path-detail="posts/win10/vb-15.png" alt="" %}

###### Click the windows popup for the inserted CD

{% include image.html path="posts/win10/vb-16.png" path-detail="posts/win10/vb-16.png" alt="" %}

###### Click Run VBoxWindowsAdditions.exe and complete the installer

{% include image.html path="posts/win10/vb-17.png" path-detail="posts/win10/vb-17.png" alt="" %}

###### Reboot
