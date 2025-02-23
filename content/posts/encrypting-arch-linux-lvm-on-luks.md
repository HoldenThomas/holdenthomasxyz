---
title: "Encrypting Arch Linux LVM on LUKS"
date: 2024-03-16T03:06:57-04:00
draft: false
tags: ["software","computer","linux"]
categories: ["tech"]
---
This guide will set up an Arch Linux system with an encrypted partition using LUKS containing logical volumes for swap, root, and home using LVM.

This is a basic guide for myself to reference. For a more in-depth guide go to the LVM on Luks section in the [arch wiki encrytion guide](https://wiki.archlinux.org/title/Dm-crypt/Encrypting_an_entire_system#LVM_on_LUKS).

Below is an example of the disk layout:
```
+----------------++-----------------------------------------------------------------------+ 
| Boot partition || Logical volume 1      | Logical volume 2      | Logical volume 3      |
|                ||                       |                       |                       | 
| /boot          || [SWAP]                | /                     | /home                 |
|                ||                       |                       |                       |
|                || /dev/MyVolGroup/swap  | /dev/MyVolGroup/root  | /dev/MyVolGroup/home  |
| (may be on     ||_ _ _ _ _ _ _ _ _ _ _ _|_ _ _ _ _ _ _ _ _ _ _ _|_ _ _ _ _ _ _ _ _ _ _ _|
| other device)  ||                                                                       |
|                ||                         LUKS2 encrypted partition                     | 
| /dev/sda1    	 ||                           /dev/sda2                                   | 
+----------------++-----------------------------------------------------------------------+ 
```
Mount Point	|Partition	        |Logical Volume			|Type				    |File System	|Size		
------------|-------------------|-----------------------|-----------------------|---------------|-----------
/mnt/boot   |/dev/partition1    |none(not encrypted)	|EFI system partition	|fat32			|1G			
[swap]		|/dev/partition2    |/dev/MyVolGroup/swap	|Linux swap	            |swap			|16G		
/mnt		|/dev/partition2    |/dev/MyVolGroup/root	|Linux root	            |btrfs			|200G		
/mnt/home	|/dev/partition2    |/dev/MyVolGroup/home	|Linux home	            |btrfs			|Remainder	


## 1. Creating the GPT partition table
List the available drives and create the partition table:
```bash
lsblk
cfdisk /dev/disk
```


## 2. Preparing the disk
Create and open the LUKS encrypted container that will contain the logical volumes:
```bash
cryptsetup luksFormat /dev/partition2
cryptsetup open /dev/partition2 cryptlvm
```

## 3. Preparing the logical volumes
Create a physical volume on top of the opened LUKS container:
```bash
pvcreate /dev/mapper/cryptlvm
```

Create a volume group and add the previously created physical volume to it:
```bash
vgcreate MyVolGroup /dev/mapper/cryptlvm
```

Create all your logical volumes on the volume group:
```bash
lvcreate -L 16G MyVolGroup -n swap
lvcreate -L 200G MyVolGroup -n root
lvcreate -l 100%FREE MyVolGroup -n home
```

Format your file systems on each logical volume:
```bash
mkfs.btrfs /dev/MyVolGroup/root
mkfs.btrfs /dev/MyVolGroup/home
mkswap /dev/MyVolGroup/swap
```

Mount your file systems:
```bash
mount /dev/MyVolGroup/root /mnt
mount --mkdir /dev/MyVolGroup/home /mnt/home
swapon /dev/MyVolGroup/swap
```


## 4. Preparing the boot partition
Create your file system for the boot partition:
```bash
mkfs.fat -F32 /dev/partition1
```


## 5. Configuration during system install
### 5.1 Packages
Install 'cryptsetup lvm2'

### 5.3 Configuring mkinitcpio
Edit `/etc/mkinitcpio.conf` adding `encrypt lvm2` to HOOKS:
```
HOOKS=(base udev autodetect modconf kms **keyboard** keymap consolefont block **encrypt** **lvm2** filesystems fsck)
```

Recreate mkinitcpio:
```bash
mkinitcpio -p
```

### 5.4 Boot loader (grub)
Exit chroot to get the UUID of the disks.
```bash
lsblk -f >> /mnt/etc/default/grub
arch-chroot /mnt
```

Edit `/etc/default/grub` and add the below to GRUB_CMLINE_DEFAULT:
```
cryptdevice=UUID=_device-UUID_:cryptlvm root=/dev/MyVolGroup/root
```

Generate grub config:
```bash
grub-mkconfig -o /boot/grub/grub.cfg
```
