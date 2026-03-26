

````markdown
# 🧾 Raspberry Pi 24/7 Stable Server Setup (Tailscale + XRDP)

This guide provides a **production-ready configuration** to keep your Raspberry Pi always reachable and stable 24/7 using Tailscale and XRDP.

---

## 🎯 Goal

- Always online Raspberry Pi ✅  
- Stable remote access via Tailscale 🔐  
- No connection drops ❌  
- Automatic recovery from network/service failures 🔧  

---

## ⚙️ 1. Enable Services at Boot

Ensure required services start automatically:

```bash
sudo systemctl enable tailscaled
sudo systemctl enable xrdp
````

---

## 🔁 2. Start Tailscale with Safe Configuration

```bash
sudo tailscale up --accept-dns=false --accept-routes=false
```

### Why?

* Prevents DNS conflicts
* Prevents routing issues
* Keeps local network as default

---

## 🌐 3. Verify Network Routing

```bash
ip route
```

Expected output:

```text
default via 192.168.50.1 dev wlan0
```

---

## 🔥 4. Keepalive (Essential)

### Use cron to maintain connection

```bash
crontab -e
```

Add:

```bash
*/2 * * * * tailscale ping 100.x.x.x > /dev/null 2>&1
```

👉 Replace `100.x.x.x` with another Tailscale device IP.

---

## 🔁 5. Tailscale Auto-Recovery (Watchdog)

Create script:

```bash
nano ~/tailscale-watchdog.sh
```

```bash
#!/bin/bash

STATUS=$(tailscale status 2>/dev/null)

if [[ -z "$STATUS" ]]; then
    echo "Tailscale down - restarting"
    systemctl restart tailscaled
    sleep 5
    tailscale up --accept-dns=false --accept-routes=false
fi
```

Make executable:

```bash
chmod +x ~/tailscale-watchdog.sh
```

Add to cron:

```bash
crontab -e
```

```bash
*/5 * * * * /home/pi/tailscale-watchdog.sh
```

---

## 📶 6. Improve Wi-Fi Stability

Disable Wi-Fi power saving:

```bash
sudo nano /etc/NetworkManager/conf.d/wifi-powersave.conf
```

Add:

```text
[connection]
wifi.powersave = 2
```

Restart NetworkManager:

```bash
sudo systemctl restart NetworkManager
```

---

## 🔌 7. Network Auto-Recovery Script

Create:

```bash
nano ~/network-watchdog.sh
```

```bash
#!/bin/bash

ping -c 1 8.8.8.8 > /dev/null 2>&1

if [ $? -ne 0 ]; then
    echo "Network down - restarting"
    systemctl restart NetworkManager
fi
```

Add to cron:

```bash
*/5 * * * * /home/pi/network-watchdog.sh
```

---

## 🖥️ 8. XRDP Auto-Restart

Ensure XRDP restarts automatically:

```bash
sudo systemctl edit xrdp
```

Add:

```ini
[Service]
Restart=always
RestartSec=5
```

Apply changes:

```bash
sudo systemctl daemon-reexec
sudo systemctl restart xrdp
```

---

## 🔐 9. Basic System Maintenance

```bash
sudo apt update && sudo apt upgrade -y
```

---

## 🧪 10. Final Checks

```bash
tailscale status
ss -tulnp
```

---

## 🧠 Final Result

With this setup, your Raspberry Pi will be:

* Always online ✅
* Automatically recovering from failures 🔄
* Stable over Wi-Fi 📶
* Accessible remotely at any time 🌍

---

## 🔥 Optional Improvements (Advanced)

* 🔐 Fail2Ban (brute-force protection)
* 🧱 UFW firewall configuration
* 🌐 Subnet routing (access entire LAN)
* 📊 Monitoring tools (uptime, logs, alerts)

---

## 💬 Conclusion

This setup transforms your Raspberry Pi into a **reliable 24/7 home server**, even behind CGNAT (e.g. mobile networks or ISPs like TIM).

---

```

