---
published: true
date: '2022-08-23 16:07 +0200'
title: 'Cisco 8000 software manageability: USB boot'
author: Frederic Cuiller
position: hidden
tags:
  - iosxr
  - Cisco 8000
  - install
  - XR7
excerpt: >-
  This tutorial will describe and illustrate how to USB boot Cisco 8000 routers.
  This step-by-step procedure can be used to stage, re-image or recover a router
  from a boot failure.
---
{% include toc icon="table" title="Cisco 8000 software manageability: USB boot" %}

## Introduction

This tutorial will describe and illustrate how to USB boot Cisco 8000 routers. This step-by-step procedure can be used to stage, re-image or recover a router from a boot failure.  
Operation is a 2-part process:
- Creation of a bootable USB drive
- USB boot invocation

**Info:** Configuration will be erased. Content of harddisk: partition will not be erased during this operation. USB boot is different than factory-reset operation.
{: .notice--primary}

## USB Drive Preparation
A bootable USB drive is created by copying a compressed boot file into a USB drive. The USB drive becomes bootable after the contents of the compressed file are extracted.

The first step is to download the compressed USB boot image from CCO:

The second step is to prepare the USB media.  
As prerequisite, storage capacity must be between 8GB (min) and 32 GB (max). USB 2.0 and USB 3.0 are supported. The disk must be formatted as FAT32 or MS-DOS file system using the Windows Operating System or Apple MAC Disk Utility. USB drive formatting is out of this article scope.

The ZIP file must be copied to the USB drive. It’s recommended to check file integrity with MD5 checksum verification:

<div class="highlighter-rouge">
<pre class="highlight">
<code>
{22-05-05 14:50}FCUILLER-M-X6F3:/Volumes/USB_8GB fcuiller% md5 8000-x64-usb-7.5.2.zip
MD5 (8000-x64-usb-7.5.2.zip) = <mark>5380c1dc23bd0484ac62338beabb5822</mark>
</code>
</pre>
</div>

File must then be extracted on the USB drive, into the root folder:

Which gives following structure:

After extraction, USB drive can be ejected and is now a bootable drive.

Info: ZIP file is not required anymore after extraction on the USB drive. Keeping it or removing it will not alter the USB boot process described next.

## Plugging USB drive

Bootable USB drive must be inserted into 8100 or 8200 USB port or 8800 RP.
Those are located on front panel:

with exception for 8201 and 8202 (rear panel):

For 8800 distributed systems, drive must be inserted on active RP in any of the two USB ports

## USB Boot Invocation
There are two ways to invoke USB boot:
- From Boot menu
- From XR CLI
Both techniques ultimately lead to the same end-result.

### From Boot menu
It’s assumed operator doesn’t have access to XR prompt (no management access or credentials unavailable) for this operation. A console access is required (115200 baud, 8 data bits, 1 stop bit, No parity).   

Right after router’s power ON, ‘Esc’ key must be pressed in continue to pause the boot process and get access to the BIOS:

If the GRUB menu shows instead, operation must be repeated to gain BIOS access:

Once in BIOS menu, operator must navigate into Boot Manager section:

Then select USB option and press enter:

After this operation, USB boot process starts:

Depending on USB drive speed, operation can take up to 10min. once process is done, router reloads:

And reboots on local disk:

As a fresh IOS-XR installation, a local administrator account must be created:

*Et voila*:

The whole process can be observed in this video (x2 speed).

### From CLI

When invoking USB boot with *reload bootmedia usb* CLI, it’s assumed operator has XR prompt access. This technique avoids BIOS menu access and directly triggers USB boot process.

<div class="highlighter-rouge">
<pre class="highlight">
<code>
Cisco-8000#reload bootmedia usb noprompt

Welcome to GRUB!!
Verifying (hd0,msdos1)/EFI/BOOT/grub.cfg...
(hd0,msdos1)/EFI/BOOT/grub.cfg verified using Pkcs7 signature.
Loading Kernel..
Verifying (loop)/boot/bzImage...
(loop)/boot/bzImage verified using attached signature.
Loading initrd..
Verifying (loop)/boot/initrd.img
</code>
</pre>
</div>

## Conclusion
This article described how USB boot can be used to stage, re-image or recover a Cisco 8000 router. While 2 different invocations techniques exist, operation results in a fresh and clean software configuration state.