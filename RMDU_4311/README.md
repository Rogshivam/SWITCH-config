# 🚀 Jio RMDU JRDU4311 — Setup & Configuration Guide

<p align="center">
  <img src="https://img.shields.io/badge/Device-JRDU4311-blue?style=for-the-badge" alt="Device JRDU4311">
  <img src="https://img.shields.io/badge/Manufacturer-VVDN-orange?style=for-the-badge" alt="Manufacturer VVDN">
  <img src="https://img.shields.io/badge/Platform-Windows-0078D4?style=for-the-badge&logo=windows&logoColor=white" alt="Windows">
</p>

<p align="center">
  <b>A community setup guide for accessing the Jio RMDU management interface and exploring switch and access point configuration.</b>
</p>

---

## 📑 Table of Contents

- [Overview](#-overview)
- [Device Information](#-device-information)
- [Requirements](#-requirements)
- [Network Topology](#-network-topology)
- [Step 1 — Connect the RMDU](#-step-1--connect-the-rmdu)
- [Step 2 — Configure Windows IPv4](#-step-2--configure-windows-ipv4)
- [Step 3 — Configure VLAN 960](#-step-3--configure-vlan-960)
- [Step 4 — Access the Web Interface](#-step-4--access-the-web-interface)
- [Step 5 — Login Credentials](#-step-5--login-credentials)
- [Troubleshooting](#-troubleshooting)
- [Next Steps](#-next-steps)
- [Disclaimer](#️-disclaimer)

---

## 📌 Overview

This guide documents the process of accessing the management interface of the **Jio JRDU4311 RMDU** using a Windows computer connected over Ethernet.

The initial objective is to establish management access. After that, the device can be inspected to determine which network configurations and operating modes are supported by its firmware.

### 🎯 Goals

- Access the RMDU web management interface.
- Configure the Windows Ethernet adapter for the management network.
- Test VLAN 960 connectivity.
- Investigate Ethernet switch and Wi-Fi access point functionality.
- Explore integration with an existing router or homelab.

> [!IMPORTANT]
> The management IP, VLAN ID, port selection, and login format below come from community-provided instructions. They have not been independently verified for every JRDU4311 firmware revision.

## 📦 Device Information

| Property | Details |
|:---|:---|
| Manufacturer | VVDN |
| Device | Jio RMDU |
| Model | JRDU4311 |
| EAN | `8908022770043` |
| Management IP (reported) | `192.168.33.1` |
| Management VLAN (reported) | `960` |

> [!NOTE]
> Avoid publishing your device's complete MAC address, serial number, or derived login password in a public repository.

## 🛠️ Requirements

Before starting, make sure you have:

- A Jio JRDU4311 RMDU
- A Windows PC or laptop
- An Ethernet cable
- A compatible Ethernet adapter that supports VLAN tagging
- A web browser

---

## 🌐 Network Topology

The initial connection should be made directly between your computer and the designated RMDU management port.

```text
       ┌──────────────────────────┐
       │       Windows PC         │
       │                          │
       │  IPv4: 192.168.33.2      │
       │  VLAN: 960 (if required) │
       └────────────┬─────────────┘
                    │
               Ethernet
                    │
       ┌────────────▼─────────────┐
       │       Jio RMDU           │
       │       JRDU4311           │
       │                           │
       │  Management IP:           │
       │  192.168.33.1             │
       └───────────────────────────┘
```

---

## 🔌 Step 1 — Connect the RMDU

1. Disconnect the RMDU from your main router for initial setup.
2. Identify the designated management port using the device labels or reliable model-specific instructions.
3. Connect your PC directly to that port using an Ethernet cable.
4. Verify that the Ethernet link light is active.

> [!WARNING]
> Do not identify the management port by position alone. Verify the port against the instructions for your particular device.

## 💻 Step 2 — Configure Windows IPv4

### Open the adapter settings

1. Press `Win + R`.
2. Enter `ncpa.cpl` and press Enter.
3. Right-click your **Ethernet** adapter.
4. Select **Properties**.
5. Open **Internet Protocol Version 4 (TCP/IPv4)**.
6. Select **Use the following IP address**.

### Enter these settings

| Setting | Value |
|:---|:---|
| IP address | `192.168.33.2` |
| Subnet mask | `255.255.255.0` |
| Default gateway | `192.168.33.1` |
| Preferred DNS | Leave blank for initial local testing |

Click **OK** to apply the settings.

### Verify the configuration

Open PowerShell:

```powershell
ipconfig
```

Confirm that the Ethernet adapter displays:

```text
IPv4 Address:    192.168.33.2
Subnet Mask:     255.255.255.0
Default Gateway: 192.168.33.1
```

---

## 🏷️ Step 3 — Configure VLAN 960

The community instructions specify **VLAN ID 960**.

### Configure the Realtek adapter

1. Open **Device Manager**.
2. Expand **Network adapters**.
3. Right-click your Ethernet adapter.
4. Select **Properties**.
5. Open the **Advanced** tab.
6. Select **VLAN ID**.
7. Enter `960` if supported by the driver.
8. Click **OK**.

### Important VLAN considerations

- The Ethernet connection may disconnect briefly when applying the setting.
- Setting the VLAN ID changes how the adapter handles VLAN traffic.
- Some drivers handle VLAN tagging differently.
- The correct VLAN configuration depends on how the RMDU management port is configured.

> [!CAUTION]
> If connectivity fails, do not assume the RMDU is defective. Verify the management port and VLAN requirements first. A port expecting untagged traffic may not work with a PC transmitting tagged frames.

---

## 🌍 Step 4 — Access the Web Interface

Open your browser and visit:

**[http://192.168.33.1](http://192.168.33.1)**

### Test connectivity

Run the following command in PowerShell:

```powershell
ping 192.168.33.1
```

Inspect the ARP table:

```powershell
arp -a
```

### Interpret the results

| Result | Meaning |
|:---|:---|
| Ping replies | The management IP responds to ICMP. |
| Request timed out | The device may be unreachable or may block ICMP. |
| Destination host unreachable | Windows cannot reach the destination through the current network path. |
| No ARP entry for `192.168.33.1` | The PC has not resolved the target's MAC address. |

> [!NOTE]
> A failed ping alone does not prove that the web interface is unavailable. Some devices block ICMP while still allowing HTTP or HTTPS.

---

## 🔐 Step 5 — Login Credentials

The community-provided instructions describe this login format:

| Field | Value |
|:---|:---|
| Username | `admin` |
| Password format | `JRDU4311` + lowercase MAC address without separators |

### Example

For a fictional MAC address:

```text
AA:BB:CC:11:22:33
```

The example password would be:

```text
JRDU4311aabbcc112233
```

**This is an illustrative example, not a real device password.** Use your own device's MAC address and verify the password format against a trusted source.

> [!WARNING]
> Do not commit your actual password, complete MAC address, serial number, or other device-specific credentials to GitHub.

---

## 🧰 Troubleshooting
g
<details>
<summary><b>❌ Ping returns "Request timed out"</b></summary>

Check the following:

- Confirm that the correct management port is being used.
- Verify the PC's IPv4 address and subnet mask.
- Check whether VLAN 960 is required on the PC-facing port.
- Confirm the RMDU management IP for your firmware.
- Try opening the web interface even if ping fails.

</details>

<details>
<summary><b>❌ Destination host unreachable</b></summary>

Run:

```powershell
ipconfig
arp -a
```

Check the Ethernet adapter's IP address and whether the RMDU's IP appears in the ARP table.

If no entry appears, investigate the physical port, VLAN tagging, and management network.

</details>

<details>
<summary><b>❌ No VLAN option is available</b></summary>

VLAN support depends on the adapter and driver. Check the manufacturer's driver documentation. A compatible VLAN-aware adapter or managed switch may be needed for testing.

</details>

<details>
<summary><b>❌ Login fails</b></summary>

- Confirm the exact model number.
- Verify the MAC address and password format.
- Check whether your firmware uses different credentials.
- Avoid factory-resetting the device without a documented recovery procedure.

</details>

---
