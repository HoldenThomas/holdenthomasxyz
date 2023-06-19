---
title: "Arch Install Guide"
date: 2023-06-15T16:04:09-04:00
draft: false
tags: ["software","computer","linux"]
categories: ["tech"]
---

# Encrypted Arch Installation
This guide will install a base arch system with a encrypted root partition.

## 1. Format and Partition the disk.
---
Format and partition the disk linux will be installed on with an encrypted
root partition. Then create and mount the filesystem.
### 1.1. Use fdisk to create *GPT* partition table.
```
fdisk /dev/<disk>
```
| Mount Point   | Partition                 | Type  | Size      |
|---------------|---------------------------|-------|-----------|
| /mnt/boot     | /dev/<efi_boot_parition>  | fat32 | 1g        |
| /mnt          | /dev/<root_partition>     | btfs  | Remainder |

### 1.2. Encrypt and open root partition.
```
cryptsetup luksFormat /dev/<root_partition>
cryptsetup open /dev/<root_partition> <name>
```

### 1.3. Create filesystem and mount partitions.
```
mkfs.fat -F32 /dev/<boot_partition>
mkfs.btfs /dev/<root_partition>
mount /dev/<root_partition> /mnt
mount --mkdir /dev/<boot_partition> /mnt/boot
```

## 2. Install essential packages.
---
Install the essential packages to the mounted partitions.
```
pacstrap -K /mnt <packages>
```
- base base-devel
- linux linux-firmware
- man-db man-pages texinfo
- git
- grub efibootmgr os-prober
- networkmanager
- cryptsetup lvm2
- vim neovim
- GPU Drivers
    - nvidia nvidia-settings
    - xf86-video-amdgpu
    - xf86-video-intel
- CPU UCode
    - intel-ucode
    - amd-ucode

## 3. Configure the system.
---
### 3.1. Chroot into the newly created system.
Generate the fstab to mount the boot and root partitions. Chroot into newly
installed system.
```
genfstab -U /mnt >> /mnt/etc/fstab
arch-chroot /mnt
```
### 3.2. Set time zone and synchronize clock.
```
ln -sf /usr/share/zoneinfo/<Region>/<City> /etc/localtime
hwclock --systohc
```
### 3.3. Set locals.
>Edit `/etc/locale.gen` and uncomment locals `en_US.UTF-8 UTF-8`.
>```
>locale-gen
>echo "LANG=en_US.UTF-8" >> /etc/locale.conf
>echo "<myhostname>" >> /etc/hostname
>```
### 3.4. Set up decryption on boot.
>Edit `/etc/mkinitcpio.conf` adding `encrypt lvm2` to HOOKS
>```
>mkinitcpio -P
>```
Exit Chroot and get the *UUID* of the partition device and decrypted device and
chroot back into the system.

We will just put the output of the command that will give the *UUID* into the file 
that needs to be edited.
```
lsblk -f >> /mnt/etc/default/grub
arch-chroot /mnt
```
Here we specify the partition device and decrypted device to decyrpt in the
bootloader's settings we will be using.
>Edit `/etc/default/grub` and add this to GRUB_CMLINE_DEFAULT
>```
>cryptdevice=UUID=<partition uuid>:<name> root=UUID=<decrypted partition uuid>
>```
### 3.5 Enabling software that needs to start at boot.
```
systemctl enable NetworkManager
```

## 4. Install the bootloader.
---
### 4.1. Install grub bootloader.
Two commands to install grub when using *efi*. The command with `--removable'
I have found is needed when installing on a system with a duel boot setup.
```
grub-install --target=x86_64-efi --efi-directory=boot --bootloader-id=GRUB
```
or
```
grub-install --target=x86_64-efi --efi-directory=boot --removable
```
### 4.2. Generating grub's config.
```
grub-mkconfig -o /boot/grub/grub.cfg
```

## 5. Creating users
### 5.1. Creating a new user and giving them *sudo* privileges.
```
useradd -m -G wheel,audio,disk,input,kvm,optical,scanner,storage,video <username>
```
>Edit sudoers using the command `$EDITOR=nvim visudo` and uncomment
>```
>%wheel ALL=(ALL:ALL) ALL
>```

The next line can also be uncomment and updated with any commands you want
entered without a password.
>Example
>```
>%wheel ALL=(ALL:ALL) NOPASSWD: /usr/bin/mount,/usr/bin/umount,/usr/bin/pacman
>```
Give root and the newly created user a password
```
passwd
passwd <username>
```
---
At this point you should be able to exit chroot and reboot your system. As well
as unmount your bootable USB.  Once booted back in you should be asked for your
encryptition password and then boot into a your newly created arch system.

Next is installing a desktop environment, wm, aur, etc.
