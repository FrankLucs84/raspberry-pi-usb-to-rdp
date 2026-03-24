# 🍓 Raspberry Pi — Local setup and control

> **Environment:** Raspberry Pi 4/5 · Debian GNU/Linux 13 (Trixie) · Kernel 6.12.x (arm64)
> **Last updated:** March 2026
> **Goal:** Clone a bootable Debian system from USB stick to micro-SD,
> configure WiFi, and set up a graphical desktop accessible from Windows via RDP.

---

## 📋 Prerequisites

- Raspberry Pi with Debian Lite booted from USB stick (`/dev/sda`)
- Micro-SD card inserted in the slot (`/dev/mmcblk0`, ≥ 64 GB recommended)
- Terminal access (via monitor + keyboard, or SSH)

---

## PHASE 1 — Verify Block Devices

```bash
lsblk
```

**Expected output:**
```
sda       8:0    1   7.3G  0 disk
├─sda1    8:1    1   512M  0 part  /boot/firmware
└─sda2    8:2    1   6.8G  0 part  /
mmcblk0 179:0    0 119.2G  0 disk
└─mmcblk0p1                        (not mounted)
```

---

## PHASE 2 — Clone USB → Micro-SD

```bash
# Unmount the SD card (if necessary)
sudo umount /dev/mmcblk0p1 2>/dev/null || true

# Bit-for-bit clone (~3–5 minutes, do NOT interrupt)
sudo dd if=/dev/sda of=/dev/mmcblk0 bs=4M status=progress conv=fsync

# Flush writes and power off
sync && sudo poweroff
```

> ⚠️ `if=` is the **source** (USB), `of=` is the **destination** (SD). Never swap them.

---

## PHASE 3 — Boot from Micro-SD

1. With the Raspberry Pi **powered off**, remove the USB stick
2. Leave only the micro-SD card in the slot
3. Power on → the system will boot from the SD card

---

## PHASE 4 — Expand the Filesystem

The clone only occupies ~7.3 GB out of the full SD card capacity. Expand the partition:

```bash
sudo raspi-config
```
→ `6 Advanced Options` → `A1 Expand Filesystem` → `OK` → `Finish` → Reboot

**Or via command line:**
```bash
sudo raspi-config --expand-rootfs
sudo reboot
```

**Verify:**
```bash
df -h /
# Expected: /dev/mmcblk0p2  117G  3.8G  109G  4%  /
```

---

## PHASE 5 — WiFi Configuration

```bash
# List available networks
sudo nmcli device wifi list

# Connect (--ask flag avoids key-mgmt bug on Debian Trixie)
sudo nmcli device wifi connect "YOUR_SSID" --ask

# Verify assigned IP address
hostname -I

# Verify internet connectivity
ping -c 4 8.8.8.8
```

> ℹ️ **Debian Trixie note:** The standard `nmcli` syntax with `password "xxx"` triggers:
> `802-11-wireless-security: key-mgmt: property is missing`.
> Always use `--ask` to enter the password interactively.

---

## PHASE 6 — System Update

```bash
sudo apt update
sudo apt upgrade -y
```

---

## PHASE 7 — Graphical Desktop + Remote Desktop Server

```bash
# Install XFCE (lightweight desktop) + xrdp (RDP server, compatible with Windows)
sudo apt install xfce4 xfce4-goodies xrdp -y

# Set XFCE as the default desktop session
echo startxfce4 > ~/.xsession

# Enable and start the xrdp service
sudo systemctl enable xrdp
sudo systemctl restart xrdp
```

---

## PHASE 8 — Connect from Windows via Remote Desktop

1. On your Windows PC: `Win + R` → type `mstsc` → Enter
2. **Computer** field: enter the Raspberry Pi's IP address (e.g. `192.168.x.x`)
3. Click **Connect**
4. SSL certificate warning → click **Yes** *(expected on a local network — self-signed certificate)*
5. Login with your Raspberry Pi credentials

> ✅ The **XFCE** desktop of the Raspberry Pi will appear on your Windows screen.

---

## 🗺️ Final Architecture

```
[Windows PC]
     │
     │  Remote Desktop Protocol (RDP — port 3389)
     │  mstsc → <raspberry-pi-local-ip>
     ▼
[Raspberry Pi — Headless Server]
  ├── /dev/mmcblk0p1  →  /boot/firmware  (512 MB)
  ├── /dev/mmcblk0p2  →  /  (117 GB · Debian GNU/Linux 13 Trixie)
  ├── WiFi: wlan0  →  connected to local network
  ├── Desktop: XFCE4
  └── Remote Access: xrdp (active and enabled at boot)
```

---

## 📌 Useful Post-Installation Commands

```bash
# xrdp service status
sudo systemctl status xrdp

# Raspberry Pi IP address
hostname -I

# Available disk space
df -h /

# System update
sudo apt update && sudo apt upgrade -y

# Manual WiFi reconnect
sudo nmcli device wifi connect "YOUR_SSID" --ask
```

---

## ⚠️ Known Troubleshooting

| Issue | Cause | Solution |
|---|---|---|
| `apt update` fails with DNS error | Network not configured | Configure WiFi before any operation |
| `nmcli` error `key-mgmt missing` | Known bug in Debian Trixie + nmcli | Use the `--ask` flag |
| `xrdp.service does not exist` | xrdp not yet installed | Run `apt install xrdp` first |
| SSL certificate warning on Windows | xrdp self-signed certificate | Expected on LAN — click "Yes" |
| `ping -c 8.8.8.8` — invalid argument | Missing count after `-c` | Correct: `ping -c 4 8.8.8.8` |
| `aptinstall: command not found` | Missing space in command | Correct: `apt install` (with space) |

---

## 💡 Technical Notes

- **dd** (Disk Duplicator): performs a bit-for-bit disk copy, preserving the bootloader, partition table, and filesystem. Never use `cp` to clone bootable drives.
- **xrdp**: open-source implementation of Microsoft's RDP protocol. Natively compatible with Windows' built-in `mstsc.exe` client — no additional software required.
- **XFCE**: lightweight desktop environment (~300 MB RAM), ideal for ARM systems like Raspberry Pi.
- **Debian Trixie**: Debian 13, stable and optimized for the ARM64 (aarch64) architecture of Raspberry Pi 4/5.

---

*Guide tested on Raspberry Pi 4/5 · Debian GNU/Linux 13 Trixie · Kernel 6.12.x (arm64)*
