---
title: "Linux Cheat Sheet"
date: 2024-03-16T19:24:16-04:00
draft: false
tags: ["software","computer","linux"]
categories: ["tech"]
---

## Pacman
```bash
pacman-key --init
pacman-key --populate
pacman-key --refresh-keys
```

## Mounting drives
### Samba shares
Install 'cifs-utils' for smb support
Edit '/etc/credentials/samba/share'
```
username=user
password=pass
```
Change permissions
```bash
chown root:root /etc/credentials
chmod 700 /etc/credentials/samba
chmod 600 /etc/credentials/samba/share
```
Edit '/etc/fstab' for mounting on boot
```
//<server>//<share> <mountpoint> cifs _netdev,nofail,credentials=/etc/credentials/samba/share,uid=user,gid=goup 0 0
```

### Veracrypt drives
Install 'ntfs-3g' for ntfs partitions
Edit '/etc/credentials/veracrypt/storage' with your password
Change permissions
```bash
chown root:root /etc/credentials
chmod 700 /etc/credentials/veracrypt
chmod 600 /etc/credentials/veracrypt/storage
```
Edit '/etc/cryttab' for decryption on boot
```
<name> /dev/<partition> /etc/credentials/veracrypt/storage tcrypt,tcrypt-veracrypt,noauto
```
Edit '/etc/fstab' for mounting on boot
```
/dev/mapper/<name> <mountpoint> ntfs3 noauto,x-systemd.automount,uid=<user>,gid=<group> 0 0
```

## youtube-dl issues
Edit /usr/lib/python3.11/site-packages/youtube_dl/extractor/youtube.py and commit out
```
'uploader_id': self._search_regex(r'/(?:channel|user)/([^/?&#]+)', owner_profile_url, 'uploader id') if owner_profile_url else None,
```

## Git
```
git submodule update --init --recursive
git submodule update --remote --merge
```
