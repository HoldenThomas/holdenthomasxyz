---
title: "Encrypting Arch Linux LUKS on LVM"
date: 2025-02-23T03:11:23-05:00
draft: false
tags: ["software","computer","linux"]
categories: ["tech"]
---
This guide will set up an Arch Linux system with a partition containing logical volumes for swap, root, and home using LVM. Which are then ecrypted using LUKS.

This is a basic guide for myself to reference. For a more in-depth guide go to the Luks on LVM section in the [arch wiki encrytion guide](https://wiki.archlinux.org/title/Dm-crypt/Encrypting_an_entire_system#LUKS_on_LVM).

Below is an example of the disk layout:
```
+----------------+-------------------------------------------------------------------------------------------------+
| Boot partition | dm-crypt plain encrypted volume | LUKS encrypted volume         | LUKS encrypted volume         |
|                |                                 |                               |                               |
| /boot          | [SWAP]                          | /                             | /home                         |
|                |                                 |                               |                               |
|                | /dev/mapper/swap                | /dev/mapper/root              | /dev/mapper/home              |
|                |_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _|_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _|_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _|
|                | Logical volume 1                | Logical volume 2              | Logical volume 3              |
|                | /dev/MyVolGroup/cryptswap       | /dev/MyVolGroup/cryptroot     | /dev/MyVolGroup/crypthome     |
|                |_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _|_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _|_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _|
|                |                                                                                                 |
|   /dev/sda1    |                                   /dev/sda2                                                     |
+----------------+-------------------------------------------------------------------------------------------------+
```

Mount Point	|Partition	        |Logical Volume			    |Type				    |File System	|Size		
------------|-------------------|---------------------------|-----------------------|---------------|-----------
/mnt/boot   |/dev/partition1    |none(not encrypted)	    |EFI system partition	|fat32			|1G			
[swap]		|/dev/partition2    |/dev/MyVolGroup/cryptswap	|Linux swap	            |swap			|16G		
/mnt		|/dev/partition2    |/dev/MyVolGroup/cryptroot	|Linux root	            |btrfs			|200G		
/mnt/home	|/dev/partition2    |/dev/MyVolGroup/crypthome	|Linux home	            |btrfs			|Remainder	

...To Be Continued

