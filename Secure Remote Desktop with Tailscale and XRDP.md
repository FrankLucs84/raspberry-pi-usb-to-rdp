
````markdown
# 🍓 Raspberry Pi - Remote access via VPN (Tailscale + XRDP)

Secure remote access to your Raspberry Pi desktop from anywhere (Wi-Fi / 4G / 5G) without configuring port forwarding.

---

## 🚀 Features

- 🔐 Secure connection (WireGuard via Tailscale)
- 🌍 Access from anywhere (including CGNAT / mobile networks)
- 🚫 No router port forwarding required
- 📱 Compatible with smartphones (Android / iOS)
- 🖥️ Remote desktop via XRDP (RDP protocol)

---

## 🧱 Prerequisites

Make sure you have:

- A Raspberry Pi with:
  - Raspberry Pi OS (Lite or Desktop)
  - Active internet connection
- A smartphone or external PC
- A Tailscale account (Google, Microsoft, or GitHub)

---

## 📦 Install Tailscale on Raspberry Pi

Run the following command:

```bash
curl -fsSL https://tailscale.com/install.sh | sh
````

---

## ⚙️ Start and Enable the Service

```bash
sudo systemctl start tailscaled
sudo systemctl enable tailscaled
```

### Verify service status:

```bash
systemctl status tailscaled
```

Expected output:

```
active (running)
```

---

## 🔐 Authenticate the Device

Run:

```bash
sudo tailscale up
```

You will receive a URL like:

```
https://login.tailscale.com/xxxx
```

### ⚠️ Important

* Do NOT open the link on the Raspberry Pi
* Open it from your phone or PC browser

---

## 📱 Authorize the Device

1. Copy the provided URL
2. Open it in a browser (phone or PC)
3. Log in with your Tailscale account
4. Click **Connect / Authorize**

---

## 🔍 Verify Connection

Run:

```bash
tailscale status
tailscale ip -4
```

Example output:

```
100.x.x.x
```

👉 This is your Raspberry Pi Tailscale IP

---

## 📱 Install Tailscale on Your Smartphone

* Android → Play Store → **Tailscale**
* iOS → App Store → **Tailscale**

Log in using the same account.

---

## 🧪 Verify Device Status

Open the Tailscale app:

* Your Raspberry Pi should appear as **Online ✅**

---

## 🖥️ Install XRDP

Install XRDP to enable remote desktop access:

```bash
sudo apt update
sudo apt install xrdp -y
sudo systemctl enable xrdp
sudo systemctl start xrdp
```

---

### 🧩 (Raspberry Pi OS Lite Only) Install Desktop Environment

If you are using the Lite version, install a lightweight desktop:

```bash
sudo apt install xfce4 xfce4-goodies -y
echo "startxfce4" > ~/.xsession
sudo systemctl restart xrdp
```

---

## 📱 Remote Desktop Apps

### Android

* **Windows App (Microsoft)**

### iOS

* **Microsoft Remote Desktop**

### Windows (PC)

* Use built-in Remote Desktop (`mstsc`)

---

## 🔗 Connect via RDP

Create a new connection and configure:

* **PC Name / Host:**

```
100.x.x.x
```

* **Username:**

```
pi
```

* **Password:**

```
<your Raspberry user password>
```

---

## ⚠️ First Connection

You may see a certificate warning:

```
Untrusted certificate → Continue
```

This is expected on first connection.

---

## 🌍 Test from External Network

To verify everything works:

1. Disable Wi-Fi on your phone
2. Enable mobile data (4G/5G)
3. Turn on Tailscale
4. Connect via Remote Desktop

---

## 🔐 Security

This setup is secure because:

* ✔️ No open ports on your router
* ✔️ End-to-end encryption (WireGuard)
* ✔️ Only authorized devices can connect

---

## 🧠 Network Architecture

```
Phone / PC ──────┐
                 │
         (WireGuard VPN)
                 │
        Tailscale Network
                 │
         Raspberry Pi
```

---

## ⚠️ Troubleshooting

| Problem          | Solution                      |
| ---------------- | ----------------------------- |
| Cannot connect   | Check `tailscale status`      |
| Wrong IP         | Use `tailscale ip -4`         |
| XRDP not working | Check `systemctl status xrdp` |
| Login failed     | Verify username/password      |

---

## 🔧 Useful Commands

```bash
tailscale status
tailscale ip -4
sudo systemctl restart tailscaled
sudo tailscale down
sudo tailscale up
```

---

## 🎯 Final Result

With this setup you get:

* 🌍 Remote access from anywhere
* 🚫 No router configuration required
* ✅ Works behind CGNAT (e.g. mobile networks, ISPs like TIM)
* 🖥️ Stable remote desktop access

---

## 🔥 Future Improvements

* Access your entire home network (subnet routing)
* SSH access without passwords
* Advanced Tailscale ACLs
* Custom hostnames (MagicDNS)

---

## 📜 License

MIT (optional)

---

## 🙌 Credits

Tested on Raspberry Pi OS with XRDP and Tailscale.

```
