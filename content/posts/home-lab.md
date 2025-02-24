---
title: "Home Lab"
date: 2025-02-24T01:51:05-05:00
draft: false
tags: ["hardware","software","gear","computer"]
categories: ["tech"]
---

## NAS Server Build
- **CPU**: [Intel Core i5-6500 3.2 GHz Quad-Core Processor](https://pcpartpicker.com/product/xwhj4D/intel-cpu-bx80662i56500)
- **CPU Cooler**: [Arctic Alpine 12 CO CPU Cooler](https://pcpartpicker.com/product/JZyV3C/arctic-alpine-12-co-cpu-cooler-acalp00031a)
- **Motherboard**: [MSI Z170A SLI PLUS ATX LGA1151 Motherboard](https://pcpartpicker.com/product/yLPzK8/msi-motherboard-z170asliplus)
- **RAM**:
    - [Corsair Vengeance LPX 8 GB (2 x 4 GB) DDR4-2400 CL16 Memory](https://pcpartpicker.com/product/HBjWGX/corsair-memory-cmk8gx4m2a2400c16)
    - [Corsair Vengeance LPX 32 GB (2 x 16 GB) DDR4-3600 CL18 Memory](https://pcpartpicker.com/product/Yg3mP6/corsair-vengeance-lpx-32-gb-2-x-16-gb-ddr4-3600-memory-cmk32gx4m2d3600c18)
- **SSD**: [SanDisk Ultra II 240 GB 2.5" Solid State Drive](https://pcpartpicker.com/product/yPbp99/sandisk-internal-hard-drive-sdssdhii240gg25)
- **M.2 SSD**: [Crucial P3 Plus 500 GB M.2-2280 PCIe 4.0 X4 NVME Solid State Drive](https://pcpartpicker.com/product/vPpzK8/crucial-p3-plus-500-gb-m2-2280-nvme-solid-state-drive-ct500p3pssd8)
- **HDD**: [Seagate IronWolf 6TB NAS HDD](https://pcpartpicker.com/product/Q7kj4D/seagate-ironwolf-nas-6-tb-35-7200rpm-internal-hard-drive-st6000vn0033) x4
- **GPU**: [MSI GAMING GeForce GTX 1060 6GB 6 GB Video Card](https://pcpartpicker.com/product/vVWrxr/msi-geforce-gtx-1060-6gb-gaming-video-card-geforce-gtx-1060-gaming-6g)
- **Case**: [NZXT S340 ATX Mid Tower Case](https://pcpartpicker.com/product/tD38TW/nzxt-case-cas340wbr1)
- **PSU**: [SeaSonic M12II 520 W 80+ Bronze Certified Fully Modular ATX Power Supply](https://pcpartpicker.com/product/TgW9TW/seasonic-power-supply-m12ii520bronze)

This was built from my old gaming pc and has gone through multiple different setups.
From TrueNAS Core to TrueNAS Scale with TrueCharts apps.
Then Proxmox running TrueNAS in a VM and portainer in a LXC container.
Then back to TrueNAS Scale.

## Mini PC
- [ACEMAGICIAN Dual LAN Mini PC](https://www.amazon.com/ACEMAGICIAN-Computers-Display-Desktop-Bluetooth/dp/B0C9J69KH8?th=1)

Currently running proxmox with just an Ubuntu server LXC container. This runs all my docker containers.

### Docker Containers
- **Portainer**: Managing docker
- **Nextcloud**: Cloud storage
- **Cloudflare-Tunnel**: Accessing Nextcloud outside my home network
- **Tailscaled**: VPN access to my home network
- **Omada-Controller**: TP-Link software for managing my WAPs
- **Homarr**: Dashboard
- **Prowlarr**: Index manager
- **Radarr**: Movie automation
- **Sonarr**: Shows automation
- **Lidarr**: Music automation
- **qBittorrent**: Torrent client
- **Gluetun**: VPN client for docker containers
- **Jellyfin**: Media client
- **Komga**: Manga client
- **Calibre**: Book management
- **Calibre-Web**: Web interface for calibre
- **Photoprism**: Photo gallery
- **Syncthing**: Sync client
- **Tubearchivist**: YouTube video archiving
- **FreshRSS**: RSS client
- **FiveFilters-Full-Text-RSS**: Creating full-text rss feeds

## UPS
- [CyberPower CP1500PFCLCD PFC Sinewave UPS System](https://www.amazon.com/CyberPower-CP1500PFCLCD-Sinewave-Outlets-Mini-Tower/dp/B00429N19W?th=1)

This is powering both boxes incase of a power outage.
