# 🐭 Mouse Ka Tools
### A Portable Wireless Security Toolkit 

A pocket-sized wireless penetration testing platform built on a GL-iNet Mango V2 router flashed with a WiFi Pineapple clone image. Designed for wireless reconnaissance, WPA2 handshake capture, and network security assessments

---

## Hardware

| Component | Model | Purpose |
|-----------|-------|---------|
| Travel Router | GL-iNet Mango V2 (GL-MT300N-V2) | Main platform |
| WiFi Adapter | Atheros AR9271 (0cf3:9271) | Monitor mode + packet injection |
| Screwdriver Set | Precision set | Hardware access |
| Power | Power bank / USB adapter (5V/1A) | Portable power |

---

## Software Stack

| Software | Version | Purpose |
|----------|---------|---------|
| OpenWrt | 19.07.7 | Base firmware |
| WiFi Pineapple Clone | 19.07.7 | Attack platform UI |
| ath9k-htc driver | via opkg | Atheros AR9271 support |
| Source | [wifi-pineapple-cloner-builds](https://gitlab.com/xchwarze/wifi-pineapple-cloner-builds) | Clone image |

---

## Setup Guide

### Prerequisites
- GL-iNet Mango V2 router
- Atheros AR9271 WiFi adapter (or compatible monitor mode adapter)
- Computer with ethernet port
- OpenWrt 19.07.7 firmware image for GL-MT300N-V2
- WiFi Pineapple clone image (19.07.7) from [xchwarze's repo](https://gitlab.com/xchwarze/wifi-pineapple-cloner-builds)

> ⚠️ **Important:** Use OpenWrt version **19.07.7 specifically**. Version 19.07.10 is NOT compatible with the Pineapple clone image. Using the wrong version requires repeating the entire flash process.

---

### Step 1 — Download the Correct Firmware

Download **OpenWrt 19.07.7** for GL-MT300N-V2 from the OpenWrt firmware selector.

Download the **WiFi Pineapple clone image (19.07.7)** from:
```
https://gitlab.com/xchwarze/wifi-pineapple-cloner-builds
```

---

### Step 2 — Flash OpenWrt via Mango Reset UI

The Mango V2 has a built-in recovery/reset UI that makes flashing straightforward:

1. Power off the Mango
2. Hold the reset button while powering on
3. Keep holding until the first two LEDS are solid
4. Connect your computer to the Mango via ethernet
5. Open browser and navigate to **192.168.1.1**
6. Upload the **OpenWrt 19.07.7** firmware image
7. Wait for flash to complete and router to reboot

> ✅ The reset UI handles everything automatically after upload — no manual intervention needed.

---

### Step 3 — Flash the Pineapple Clone Image

Once OpenWrt 19.07.7 is running:

1. Connect to the Mango via ethernet
2. Access LuCI at **192.168.1.1** or SSH in:
```bash
ssh root@192.168.1.1
```
3. Set a password
4. Navigate to **System → Backup/Flash Firmware**
5. Upload the Pineapple clone image
6. Wait for reboot

> ✅ The Pineapple clone image on OpenWrt 19.07.7 has significantly more overlay space than stock GL.iNet firmware — enough for driver installation without any storage workarounds.

---

### Step 4 — Connect Mango to Internet

Before installing drivers the Mango needs internet access. Connect via **WAN port**:

1. Plug ethernet cable from your home router to the Mango's **WAN port**
2. SSH into the Mango:
```bash
ssh root@192.168.1.1
```
3. If internet isn't working check firewall configuration:
```bash
# Check firewall rules
iptables -L

# Restart network
/etc/init.d/network restart

# Test connectivity
ping google.com
```

> ⚠️ **Known issue:** Firewall rules may block WAN traffic by default after flashing the clone image. Configure firewall to allow WAN forwarding if ping fails.

---

### Step 5 — Install Atheros AR9271 Drivers

Once internet is working via WAN:

```bash
# Update package list
opkg update

# Install ath9k-htc driver
opkg install kmod-ath9k-htc
```

Plug in the Atheros AR9271 adapter and verify:
```bash
# Check if recognized
lsusb

# Check wireless interfaces
iwconfig

# Should show new wireless interface
ip link show
```

---

### Step 6 — Verify Reconnaissance Capability

With the Atheros adapter plugged into the Mango's USB port:

```bash
# Put adapter in monitor mode
ip link set wlan1 down
iw dev wlan1 set type monitor
ip link set wlan1 up

# Start scanning
iwlist wlan1 scan
```

The Atheros adapter handles monitoring and displays nearby WiFi networks — confirming the full setup is working. 🎯

---

## Architecture

```
Power Bank (5V/1A)
       |
  [Mango V2]
  OpenWrt 19.07.7
  Pineapple Clone
       |
  [Atheros AR9271] -----> Monitor Mode + Packet Injection
  (USB plugged in)        Wireless Reconnaissance
```

---

## Capabilities

| Feature | Status |
|---------|--------|
| Wireless reconnaissance | ✅ Working |
| Monitor mode | ✅ Working via Atheros |
| Packet injection | ✅ Working via Atheros |
| Portable (power bank) | ✅ Working |
| Standalone operation | ✅ No laptop needed for recon |
| Pineapple clone UI | ✅ Working |

---

## Troubleshooting

### Wrong OpenWrt Version
**Problem:** Installed OpenWrt 19.07.10 — Pineapple clone image incompatible.
**Solution:** Must use OpenWrt **19.07.7** specifically. Repeat flash process using reset UI with correct version.

### Internet Not Working After Flash
**Problem:** Mango connected to router via WAN but no internet access.
**Solution:** Check firewall configuration. Default rules after clone flash may block WAN traffic. Configure iptables or restart network services.

### Atheros Not Recognized
**Problem:** lsusb doesn't show Atheros adapter.
**Solution:** Run `opkg install kmod-ath9k-htc` — driver not included by default. Requires internet access via WAN first.

---

## Field Reports

| Target | Date | Result |
|--------|------|--------|
| KAYLAN (Home Network) | March 2026 | [Full Report](./reports/KAYLAN_Pentest_Report.docx) |

---

## Important Notes

> ⚠️ **Legal Warning:** This toolkit is for authorized security testing only. Only use on networks you own or have explicit written permission to test. Unauthorized network access is illegal under Kenya's Computer Misuse and Cybercrimes Act 2018 and equivalent laws globally.

> 📋 **Ethical Use:** Always obtain informed consent from all network users before conducting any wireless security tests.

---

## Related Projects

- [wazuh-home-lab](https://github.com/Th3DarkAngel/project-odesa/blob/main/README.md) — Blue team SIEM home lab

---

## Credits & Acknowledgements

- **[xchwarze](https://gitlab.com/xchwarze)** — Creator of the [wifi-pineapple-cloner-builds](https://gitlab.com/xchwarze/wifi-pineapple-cloner-builds) project that made this setup possible. Without their work building and maintaining the Pineapple clone images, this toolkit would not exist.
- **OpenWrt Project** — The open source firmware powering the Mango V2
- **Hak5** — Original WiFi Pineapple concept and platform

---

## Author

- GitHub: [th3darkangel](https://github.com/th3darkangel)
- Email: bayacaxton@gmail.com

> *"The quieter you become, the more you can hear."*
