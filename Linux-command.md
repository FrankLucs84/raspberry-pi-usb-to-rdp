
````markdown
# 🐧 Linux Command Cheat Sheet (Raspberry Pi / Home Server)

This document provides a practical reference of the most important Linux commands used in this project, with a focus on:

- service management (systemd)
- networking
- remote access (SSH / XRDP)
- Tailscale VPN
- system monitoring and troubleshooting

---

## ⚙️ Service Management (systemd)

### 🔍 Check service status

```bash
systemctl status <service>
````

Examples:

```bash
systemctl status xrdp
systemctl status tailscaled
```

---

### ▶️ Start / Stop / Restart services

```bash
sudo systemctl start <service>
sudo systemctl stop <service>
sudo systemctl restart <service>
```

---

### 🔄 Enable / Disable at boot

```bash
sudo systemctl enable <service>
sudo systemctl disable <service>
```

---

### 📋 List running services

```bash
systemctl list-units --type=service --state=running
```

---

### 📋 List enabled services

```bash
systemctl list-unit-files --state=enabled
```

---

### 🧹 Reset failed services

```bash
sudo systemctl reset-failed
```

---

## 🌐 Networking

### 📡 Show network interfaces

```bash
ip a
```

---

### 🛣️ Show routing table

```bash
ip route
```

---

### 🌍 Test connectivity

```bash
ping -c 3 8.8.8.8
```

---

### 🔎 Show open ports and listening services

```bash
sudo ss -tulnp
```

Filter examples:

```bash
sudo ss -tulnp | grep 3389
sudo ss -tulnp | grep 22
```

---

### 🌐 Get local IP address

```bash
hostname -I
```

---

### 🌍 Check public IP

```bash
curl ifconfig.me
```

---

## 🔐 SSH (Secure Shell)

### Connect to a device

```bash
ssh user@192.168.x.x
ssh user@100.x.x.x
```

---

### Copy files

```bash
scp file.txt user@host:/path
```

---

## 🖥️ XRDP (Remote Desktop)

### Check XRDP status

```bash
systemctl status xrdp
```

---

### Restart XRDP

```bash
sudo systemctl restart xrdp
```

---

### Check RDP port (3389)

```bash
sudo ss -tulnp | grep 3389
```

---

## 🔐 Tailscale (VPN)

### Check connection status

```bash
tailscale status
```

---

### Get Tailscale IP

```bash
tailscale ip -4
```

---

### Start Tailscale

```bash
sudo tailscale up
```

---

### Stop Tailscale

```bash
sudo tailscale down
```

---

### Restart service

```bash
sudo systemctl restart tailscaled
```

---

## 📦 Package Management (APT)

### Update system

```bash
sudo apt update
sudo apt upgrade -y
```

---

### Install package

```bash
sudo apt install <package> -y
```

---

### Remove package

```bash
sudo apt remove <package>
```

---

### Full cleanup

```bash
sudo apt autoremove -y
```

---

## 📊 System Monitoring

### CPU / Processes

```bash
top
htop
```

---

### Memory usage

```bash
free -h
```

---

### Disk usage

```bash
df -h
```

---

### System uptime

```bash
uptime
```

---

## 📂 Logs & Debugging

### System logs

```bash
journalctl -xe
```

---

### Service logs

```bash
journalctl -u xrdp
journalctl -u tailscaled
```

---

### Boot logs

```bash
journalctl -b
```

---

## 🔍 Process Management

### List processes

```bash
ps aux
```

---

### Find process

```bash
ps aux | grep <name>
```

---

### Kill process

```bash
kill -9 <PID>
```

---

## 🧹 System Cleanup & Checks

### Check active VPN / network interfaces

```bash
ip link
```

---

### Check for VPN-related services

```bash
systemctl list-units | grep -E "vpn|wg|tailscale"
```

---

### Check installed VPN packages

```bash
dpkg -l | grep -E "wireguard|tailscale|openvpn"
```

---

## 🔥 Useful Debug Workflow

When something doesn't work:

1. Check service:

```bash
systemctl status <service>
```

2. Check ports:

```bash
sudo ss -tulnp
```

3. Check logs:

```bash
journalctl -u <service>
```

4. Check network:

```bash
ip a
ip route
```

---

## 💡 Pro Tips

* Always verify services with `systemctl status`
* Use `ss -tulnp` to debug networking issues
* Logs (`journalctl`) are your best friend
* Prefer Tailscale IP (`100.x.x.x`) for remote access
* Avoid exposing services directly to the internet

---

```

