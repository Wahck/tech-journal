# pfSense & UniFi Network Setup

![Project Logo](link-to-logo)  
**Setting up a secure and optimized home network using pfSense as the firewall and UniFi APs for WiFi. Includes VLANs, UniFi Controller placement, and network segmentation.**

## 📌 Table of Contents
- [Overview](#overview)
- [Hardware & Software Used](#hardware--software-used)
- [Installation & Setup](#installation--setup)
- [Configuration](#configuration)
- [Usage & Features](#usage--features)
- [Troubleshooting & Notes](#troubleshooting--notes)
- [Future Enhancements](#future-enhancements)
- [License](#license)

---

## 📖 Overview
This project details the setup of **pfSense as the primary firewall and router** while leveraging **UniFi U6 Lite APs** for WiFi coverage. It includes:
- VLAN segmentation for network security.
- UniFi Controller considerations (Docker vs dedicated hardware).
- Network performance and security optimizations.

## 🛠 Hardware & Software Used
| Component | Details |
|-----------|---------|
| **Router/Firewall** | Mini PC running pfSense (Intel N100) |
| **Access Points** | 2x UniFi U6 Lite |
| **UniFi Controller** | Docker on HP ProBook (considering alternatives) |
| **Switches** | Managed VLAN-capable switches (TP-Link 28-port and 8-port) |
| **Software** | pfSense, UniFi Controller, VLAN configurations, UnRaid/Docker |
| **Dependencies** | UnRaid/Docker (if running UniFi Controller in a container) |

## 🚀 Installation & Setup
### 1️⃣ Install & Configure pfSense
1. **Flash pfSense to your mini PC** and install it.
2. **Set up WAN and LAN interfaces** in pfSense.
3. **Create VLANs** in pfSense for network segmentation (e.g., IoT, Guest, Trusted devices).
4. **Enable DHCP servers** for each VLAN.

### 2️⃣ Set Up VLAN Tagging & Trunking on Switches
1. **Access your managed switch’s web interface.**
2. **Create VLANs** corresponding to the ones in pfSense.
3. **Assign VLAN tags to ports**:
   - **Trunk ports** (connecting to pfSense and APs) should allow multiple VLANs.
   - **Access ports** (connecting to devices) should be assigned to a single VLAN.
4. **Enable 802.1Q VLAN tagging** for trunk ports.

### 3️⃣ Set Up UniFi APs
1. **Connect UniFi APs to the VLAN-aware switch**.
2. **Adopt the APs in UniFi Controller** (running in Docker or another environment).
3. **Configure WiFi networks**, ensuring VLANs are properly assigned.
4. **Optimize WiFi settings** for performance and stability.

## ⚙ Configuration
### VLAN Setup Example (pfSense)
```yaml
VLAN 10 - Trusted (192.168.10.0/24)
VLAN 20 - IoT Devices (192.168.20.0/24)
VLAN 30 - Guest WiFi (192.168.30.0/24)
VLAN 40 - IoT (No Internet) (192.168.40.0/24)
```
Firewall rules should restrict IoT and Guest networks from accessing Trusted resources.

### Switch VLAN Tagging Example
```yaml
Port 1 (pfSense) -> Trunk, VLANs 10, 20, 30, 40
Port 2 (UniFi AP) -> Trunk, VLANs 10, 20, 30, 40
Port 3 (IoT Device with Internet) -> Access, VLAN 20
Port 4 (IoT Device No Internet) -> Access, VLAN 40
Port 5 (Guest WiFi AP) -> Access, VLAN 30
```

### UniFi VLAN Assignment Example
```yaml
SSID: HomeWiFi -> VLAN 10
SSID: IoTWiFi -> VLAN 20
SSID: GuestWiFi -> VLAN 30
SSID: IoTNoInternet -> VLAN 40
```

## 🎛 Usage & Features
- **Secure Network Segmentation:** VLANs prevent IoT devices from accessing personal devices.
- **Centralized AP Management:** UniFi Controller allows remote monitoring and adjustments.
- **Optimized WiFi Performance:** Tweaked settings for signal stability and reduced interference.
- **IoT VLAN with No Internet:** Devices restricted from accessing external networks for security.

## 🛑 Troubleshooting & Notes
| Issue | Solution |
|-------|---------|
| VLAN devices not getting IPs | Ensure DHCP is enabled for each VLAN in pfSense |
| UniFi AP not adopting | Check UniFi Controller connection and inform URL |
| IoT devices cannot reach the internet | Ensure they are on VLAN 20, not VLAN 40 |
| Devices on VLANs can't communicate | Check switch VLAN tagging & trunking setup |

## 📌 Future Enhancements
- Consider a dedicated UniFi Cloud Key or separate controller instance.
- Automate backups of pfSense and UniFi configurations.
- Explore deeper DPI monitoring and firewall optimization.

## 📝 License
This work is licensed under a [Creative Commons Attribution 4.0 International License](https://creativecommons.org/licenses/by/4.0/).
