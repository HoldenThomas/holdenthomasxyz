---
title: "Arch Install Guide"
date: 2023-06-15T16:04:09-04:00
draft: false
tags: ["software","computer","linux"]
categories: ["tech"]
---

# Encrypted Arch Installation

This guide will install a base arch system with a encrypted root partition.

Partition the disk.
```
fdisk /dev/<disk>
```
Encrypt root partition.
```
cryptsetup luksFormat /dev/<root_partition>
cryptsetup open /dev/<root_partition> <name>
```
Create filesystem and mount partitions.
```
mkfs.fat -F32 /dev/<boot_partition>
mkfs.btfs /dev/<root_partition>
mount /dev/<root_partition> /mnt
mount --mkdir /dev/<boot_partition> /mnt/boot
```
Install essential packages.
```
pacstrap -K /mnt base base-devel linux linux-firmware man-db man-pages texinfo \
git grub efibootmgr networkmanager cryptsetup lvm2 vim neovim
```
- GPU Drivers
    - nvidia nvidia-settings
    - xf86-video-amdgpu
    - xf86-video-intel
- CPU ucode
    - intel-ucode
    - amd-ucode

Configure the system.
```
genfstab -U /mnt >> /mnt/etc/fstab
arch-chroot /mnt
ln -sf /usr/share/zoneinfo/<Region>/<City> /etc/localtime
hwclock --systohc
```
Edit `/etc/locale.gen` and uncomment locals `en_US.UTF-8 UTF-8`.
```
locale-gen
echo "LANG=en_US.UTF-8" >> /etc/locale.conf
echo "<myhostname>" >> /etc/hostname
```
Edit `/etc/mkinitcpio.conf` adding `encrypt lvm2` to HOOKS
```
mkinitcpio -P
```
Exit Chroot
```
lsblk -f >> /mnt/etc/default/grub
arch-chroot /mnt
```
Edit `/etc/default/grub` and add this to GRUB_CMLINE_DEFAULT
```
cryptdevice=UUID=<partition uuid>:<name> root=UUID=<decrypted partition uuid>
```
Install Grub Bootloader
```
grub-install --target=x86_64-efi --efi-directory=boot --bootloader-id=GRUB
```
or
```
grub-install --target=x86_64-efi --efi-directory=boot --removable
```
```
grub-mkconfig -o /boot/grub/grub.cfg
```
```
systemctl enable NetworkManager
useradd -m -G wheel,audio,disk,input,kvm,optical,scanner,storage,video <username>
$EDITOR=nvim visudo
passwd
passwd <username>
```
