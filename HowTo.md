# ⚡ Homelab Quickstart – My Actual Setup

This guide documents how I personally set up my homelab using a Dell Inspiron 13, external hard drive, and my MacBook as a client. It reflects my real setup steps—not just generic instructions.

---

## 🧱 1. Prepare the Homelab Server (Dell Inspiron)

- Installed **Ubuntu Server**
- Connected to **Wi-Fi** (no Ethernet used)
- Plugged in and mounted a **1TB external hard drive**

```bash
sudo mkdir -p /mnt/external_drive
sudo mount /dev/sdX1 /mnt/external_drive
```


---

## 🌐 2. Set Up Networking with Tailscale

Installed [Tailscale](https://tailscale.com/download) on both:

- 🖥 Homelab server (Ubuntu)
- 💻 MacBook Air (M1)

```bash
curl -fsSL https://tailscale.com/install.sh | sh
sudo tailscale up
```

Tested connection between devices using the Tailscale IP.

---

## 📦 3. Set Up Docker Services

First, I installed Docker & Docker Compose:

```bash
sudo apt update
sudo apt install docker.io docker-compose
```

Then I used the combined Docker Compose file located in `/docker/docker-compose.yml`:

```bash
cd docker
docker compose up -d
```

This starts both services:

- **MeTube** at `http://<tailscale-ip>:8081` for downloading media + subtitles
- **Jellyfin** at `http://<tailscale-ip>:8096` for streaming downloaded media

---

## 🎮 4. Share Emulator Files via Samba

Installed Samba:

```bash
sudo apt install samba
```

Then I added this to `/etc/samba/smb.conf`:

```ini
[games]
  path = /mnt/external_drive/games
  browseable = yes
  read only = no
  guest ok = yes
```

Restarted the Samba service:

```bash
sudo systemctl restart smbd
```

On my MacBook, I connected to the share using Finder → `⌘K → smb://<homelab-ip>/games`

---

## 🧪 5. Run Emulators on MacBook

I mounted the SMB share from the homelab and ran emulators **locally** on my MacBook using:

- DuckStation (PS1)
- PCSX2 (PS2)
- RPCS3 (PS3, limited performance)

Performance was smooth for everything except RPCS3. RPCS3 still works but not as smooth as the other emulators, but it could be related to the computer.

---

## ✅ What’s Next

- Auto-mounting the external drive via `/etc/fstab`
- Hosting Websites

---

> This homelab is Wi-Fi-only, built on minimal hardware, and fully customized to my learning workflow.