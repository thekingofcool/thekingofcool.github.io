---
layout: post
title:  "Create Bootable Windows 11 USB Disk on MacOS"
date:   2024-08-24
categories: blog
---
#### Utilities - Disk Utility
Erase your USB External Physical Volume to MS-DOS (FAT32);
Rename the USB Drive name to WIN11

#### Download Window 11 IOS
Download [Windows 11](https://www.microsoft.com/software-download/windows11) Disk Image (ISO);

#### Install wimlib
```
brew install wimlib
```

#### Mount the ISO file in a folder
```
hdiutil mount Downloads/Win11_23H2_English_x64v2.iso
```

#### Copy all Windows ISO contents into the USB Drive
```
rsync -vha --exclude=sources/install.wim /Volumes/CCCOMA_X64FRE_EN-US_DV9/* /Volumes/WIN11
```

#### Split `install.wim` file in two parts if the size is bigger than 4GB
```
wimlib-imagex split /Volumes/CCCOMA_X64FRE_EN-US_DV9/sources/install.wim /Volumes/WIN11/sources/install.swm 3800
```

#### Eject USB device to use it for OS installation
```
diskutil eject /dev/disk2
```