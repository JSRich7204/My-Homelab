# 🏡 My Homelab Journey

This repository documents how I turned my old Dell Inspiron laptop into a flexible and secure homelab. It includes step-by-step notes on setting up remote access, file sharing, MeTube-powered media downloads, game emulator access, and a self-hosted Jellyfin media server—all tailored for Wi-Fi-only environments and seamless MacBook integration.

This project serves as both a learning log and a public portfolio as I grow my skills in networking, self-hosted services, and Linux system administration.

---

## 📚 Table of Contents
- [Hardware Specs](#-homelab-hardware-specs)
- [Network Setup](#-network-setup)
- [Jellyfin Media Server](#-jellyfin-media-server-setup)
- [Emulator Streaming](#-emulator-streaming-from-homelab)
- [MeTube Media Downloader](#-metube-media-downloader)

---

## 🧱 Homelab Hardware Specs

### 🖥️ Main Server
- **Model:** Dell Inspiron 13 7000 2-in-1
- **Processor:** Intel Core i5 (8th Gen)
- **RAM:** 8 GB DDR4
- **Storage:** 256 GB SSD
- **OS:** Ubuntu Server (version TBD)
- **Power:** Always plugged in (acts as 24/7 host)
- **Form Factor:** Laptop
  
### 💻 Client: MacBook Air (M1)

- **Model:** MacBook Air (Apple Silicon, M1)
- **OS:** macOS Sequoia
- **RAM:** 8 GB Unified Memory
- **Storage:** 256 GB SSD
- **Network:** Wi-Fi only (802.11ac)
- **Use Case:** Remote access, file mounting (Samba), local emulation, Jellyfin streaming

### 🌐 Network Interface
- **Wi-Fi:** Integrated (primary connection)
- **Ethernet:** *Not used*

### 🖧 Clients
- **MacBook Air (M1)** – for streaming, remote access, and media control
- **Secondary Laptop** (used for transfer/setup)

### 📦 Peripherals
- External hard drive (4 TB, used for Jellyfin and MeTube downloads)
- USB drives (media transfer)
- Surge protector + power switch

---

## 🌐 Network Setup

### 🔐 VPN / Remote Access
- **Tailscale** used for secure access across all devices
- Installed on:
  - Dell Homelab Server
  - MacBook Air (client)

#### Tailscale Setup Notes
```bash
curl -fsSL https://tailscale.com/install.sh | sh
sudo tailscale up --authkey <YOUR-AUTH-KEY>
```
- Created a subnet route to access other LAN devices
- Enabled MagicDNS for easier access via hostname

### 🖧 Local IP Management
- Static IP assigned to homelab via router settings (DHCP reservation)
- Tailscale IP used when away from home

### 📡 Firewall / Router Notes
- UPnP disabled
- Port forwarding NOT required (Tailscale handles tunneling)
- Firewall allows Tailscale range only

---

## 🍿 Jellyfin Media Server Setup

### 📌 Goals
- Host and stream downloaded media (drama, anime, movies)
- Support subtitle files (SRT, embedded)
- Access on MacBook via browser or app

### 🐳 Docker Compose Setup
```yaml
version: "3.8"
services:
  jellyfin:
    image: jellyfin/jellyfin
    container_name: jellyfin
    network_mode: host
    volumes:
      - /path/to/media:/media
      - /path/to/config:/config
    restart: unless-stopped
```

### 🧪 Testing
- Access via `http://<tailscale-ip>:8096`
- Auto-detects most metadata
- Embedded or `.srt` subtitle files are picked up automatically

### 📺 Client Access
- **MacBook:** Used Chrome + Tailscale login

### 🛠️ Issues Encountered
- Subtitle encoding mismatch (resolved by setting UTF-8)
- Slow library scan (resolved by limiting folders scanned)

---

## 🎮 Emulator Streaming from Homelab

### ⚙️ Goal
Access game emulators hosted on the homelab server from a MacBook client using network file sharing.

### 🧩 Emulator Stack
- **PCSX2** (PS2)
- **RPCS3** (PS3)
- **DuckStation** (PS1)

### 📡 File Sharing Method
- **Samba (SMB)** used to expose `/games` directory from homelab server
- MacBook connects via Finder (⌘K > smb://[homelab-ip])
- Games and BIOS files accessed directly from mounted volume

#### Samba Configuration Snippet (`/etc/samba/smb.conf`)
```ini
[games]
   path = /home/username/games
   browseable = yes
   read only = no
   guest ok = yes
```

#### Mounting on MacBook
1. Open Finder > Go > Connect to Server…
2. Enter `smb://<homelab-ip>`
3. Mount the `games` share

### 🧪 Testing
- Ran emulators locally on MacBook using games from mounted network drive
- Verified no input lag or load issues with PS1/PS2 titles

### 🧱 Performance Notes
- Streaming via SMB is stable over Wi-Fi for retro and mid-gen consoles
- For PS3/modern consoles, consider using a laptop that has more than 8GB RAM for faster performance

---

## 📥 MeTube Media Downloader

### 🎯 Purpose
A web-based frontend for downloading media (YouTube, Dailymotion, etc.) with support for subtitles and format customization. Ideal for populating your Jellyfin media server.

### 🐳 Docker Compose Example
```yaml
version: "3"
services:
  metube:
    image: aleksilassila/metube
    container_name: metube
    ports:
      - 8081:8081
    volumes:
      - /path/to/downloads:/downloads
    restart: unless-stopped
```

### 🔧 Configuration Notes
- Downloads placed into shared Jellyfin media folder (`/downloads`)
- Subtitle files (.srt) are fetched automatically if available
- Uses `yt-dlp` under the hood for more reliable downloading

### 🧪 Usage
- Open browser to `http://<tailscale-ip>:8081`
- Paste video URL
- Select format (e.g., best + subtitles)
- Download directly to Jellyfin library

### 🛠 Troubleshooting
- If subtitle fetch fails, check subtitle language availability
- To download only subtitles: set format option to `--write-subs --skip-download`

---

> Built as part of my Computer Engineering & CS journey, this homelab is more than a server—it's my digital playground.
