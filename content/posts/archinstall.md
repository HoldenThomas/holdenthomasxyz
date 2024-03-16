---
title: "Arch Install Guide"
date: 2023-06-15T16:04:09-04:00
draft: false
tags: ["software","computer","linux"]
categories: ["tech"]
---

This guide will install a base arch system with a encrypted root partition.

## 1. Format and Partition the disk
---
Below is an example of the disk layout:
Mount Point	|Partition	                |Type				    |File System	|Size		
------------|---------------------------|-----------------------|---------------|-----------
/mnt/boot   |/dev/efi_system_partition	|EFI system partition	|fat32			|1G			
[swap]		|/dev/swap_partition	    |Linux swap	            |swap			|16G		
/mnt		|/dev/root_partition	    |Linux root	            |btrfs			|200G		
/mnt/home	|/dev/home_partition	    |Linux home	            |btrfs			|Remainder	

### 1.1. Create GPT partition table
List the available drives and create the partition table:
```
lsblk
cfdisk /dev/disk
```


### 1.2. Create filesystem and mount partitions
```
mkfs.fat -F32 /dev/efi_system_partition
mkfs.btfs /dev/root_partition
mkfs.btfs /dev/home_partition
mount /dev/root_partition /mnt
mount --mkdir /dev/efi_system_partition /mnt/boot
mount --mkdir /dev/home_partition /mnt/home
```


## 2. Install essential packages
---
```
pacstrap -K /mnt <packages>
```
- base base-devel
- linux linux-firmware
- man-db man-pages texinfo
- git
- grub efibootmgr os-prober
- networkmanager
- vim neovim
- GPU Drivers
    - nvidia nvidia-settings
    - xf86-video-amdgpu
    - xf86-video-intel
- CPU UCode
    - intel-ucode
    - amd-ucode


## 3. Configure the system
---
### 3.1. Fstab and Chroot
```
genfstab -U /mnt >> /mnt/etc/fstab
arch-chroot /mnt
```

### 3.2. Time
```
ln -sf /usr/share/zoneinfo/<Region>/<City> /etc/localtime
hwclock --systohc
```

### 3.3. Localization
Edit `/etc/locale.gen` and uncomment `en_US.UTF-8 UTF-8`.
```
locale-gen
echo "LANG=en_US.UTF-8" >> /etc/locale.conf
echo "<myhostname>" >> /etc/hostname
```

### 3.3. Enabling systemd units
```
systemctl enable NetworkManager
```

## 4. Install the bootloader
---
### 4.1. Install grub bootloader
The command with `--removable' I have found is needed when installing on a system with a duel boot setup.
```
grub-install --target=x86_64-efi --efi-directory=boot --bootloader-id=GRUB
```
or
```
grub-install --target=x86_64-efi --efi-directory=boot --removable
```

### 4.2. Generating grub config
```
grub-mkconfig -o /boot/grub/grub.cfg
```

## 5. Creating users
```
useradd -m -G wheel,audio,disk,input,kvm,optical,scanner,storage,video <username>
```
Edit sudoers using the command `$EDITOR=nvim visudo` and uncomment:
```
%wheel ALL=(ALL:ALL) ALL
```

The next line can also be uncomment and updated with any commands you want
entered without a password.
Example
```
%wheel ALL=(ALL:ALL) NOPASSWD: /usr/bin/mount,/usr/bin/umount,/usr/bin/pacman
```

Give root and the newly created user a password
```
passwd
passwd <username>
```
---
Exit chroot, reboot your system, and remove your installation usb.  
