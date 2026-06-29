# Day 09 - Interface Configuration & Device Management

## Overview

Today's CCNA lab focused on interface configuration and management across Cisco routers and switches.

Rather than simply assigning IP addresses, this lab introduced several real-world network administration tasks including manually configuring interface speed and duplex settings, assigning interface descriptions, verifying interface status, and disabling unused interfaces to improve network security and reduce unnecessary attack surfaces.

These are common administrative tasks performed by network engineers when deploying and maintaining enterprise networks.

---

## Network Topology

<p align="center">
<a href="PASTE-IMAGE-LINK-HERE">
<img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2009%20Lab%20-%20Interface%20Configuration.png" width="1200">
</a>
</p>

Topology consisting of one Cisco 2911 router, two Cisco 2960 switches, and four end devices operating within the 172.16.0.0/16 network.

---

# Lab Objectives

During this lab I completed the following tasks:

- Configure hostnames
- Configure IPv4 addressing
- Configure interface speed
- Configure duplex settings
- Configure interface descriptions
- Verify interface status
- Disable unused interfaces
- Test network connectivity
- Practice Cisco IOS management commands

---

# Network Design

## Address Space

```
172.16.0.0/16
```

### Router

```
R1 G0/0

172.16.255.254
```

---

### Hosts

| Device | IP Address |
|----------|------------|
| PC1 | 172.16.0.1 |
| PC2 | 172.16.0.2 |
| PC3 | 172.16.0.3 |
| PC4 | 172.16.0.4 |

Gateway

```
172.16.255.254
```

---

# Step 1 — Configure Device Hostnames

<p align="center">
<a href="PASTE-IMAGE-LINK-HERE">
<img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2009%20Lab%20-%20Interface%20Configuration-1.png" width="1200">
  <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2009%20Lab%20-%20Interface%20Configuration-1.2.png" width="1200">
  <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2009%20Lab%20-%20Interface%20Configuration-1.3.png" width="1200">
</a>
</p>

Each network device was assigned an appropriate hostname.

Configured devices

- R1
- SW1
- SW2

Commands

```Cisco
hostname R1

hostname SW1

hostname SW2
```

Using descriptive hostnames greatly simplifies troubleshooting in enterprise environments.

---

# Step 2 — Configure Router Interface

<p align="center">
<a href="PASTE-IMAGE-LINK-HERE">
<img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2009%20Lab%20-%20Interface%20Configuration-2.1.png" width="1200">
</a>
</p>

The router's GigabitEthernet0/0 interface was configured with the gateway address for the LAN.

Commands

```Cisco
interface g0/0

ip address 172.16.255.254 255.255.0.0

no shutdown
```

After configuration the interface status successfully transitioned to:

```
UP

UP
```

---

# Step 3 — Configure Host IPv4 Addresses

Each workstation was manually configured.

---

## PC1

<p align="center">
<a href="PASTE-IMAGE-LINK-HERE">
<img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2009%20Lab%20-%20Interface%20Configuration-2.2.png" width="1200">
</a>
</p>

```
IP

172.16.0.1

Gateway

172.16.255.254
```

Connectivity successfully verified.

---

## PC2

<p align="center">
<a href="PASTE-IMAGE-LINK-HERE">
<img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2009%20Lab%20-%20Interface%20Configuration-2.3.png" width="1200">
</a>
</p>

```
172.16.0.2
```

Gateway connectivity verified.

---

## PC3

<p align="center">
<a href="PASTE-IMAGE-LINK-HERE">
<img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2009%20Lab%20-%20Interface%20Configuration-2.4.png" width="1200">
</a>
</p>

```
172.16.0.3
```

Gateway successfully reached.

---

## PC4

<p align="center">
<a href="PASTE-IMAGE-LINK-HERE">
<img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2009%20Lab%20-%20Interface%20Configuration-2.5.png" width="1200">
</a>
</p>

```
172.16.0.4
```

Connectivity confirmed.

---

# Step 4 — Verify Router Interfaces

<p align="center">
<a href="PASTE-IMAGE-LINK-HERE">
<img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2009%20Lab%20-%20Interface%20Configuration-3.1.png" width="1200">
</a>
</p>

Using

```Cisco
show ip interface brief
```

I verified the operational status of the configured interfaces.

This command allows engineers to quickly identify:

- Interface status
- Assigned IP addresses
- Administrative state
- Protocol state

---

# Step 5 — Configure Interface Speed & Duplex

<p align="center">
<a href="PASTE-IMAGE-LINK-HERE">
<img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2009%20Lab%20-%20Interface%20Configuration-3.2.png" width="1200">
  <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2009%20Lab%20-%20Interface%20Configuration-3.3.png" width="1200">
  <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2009%20Lab%20-%20Interface%20Configuration-3.4.png" width="1200">
</a>
</p>

To simulate enterprise deployments, switch uplink interfaces were manually configured.

Commands

```Cisco
speed 1000

duplex full
```

These settings were applied to switch-to-switch and router-to-switch links.

---

# Step 6 — Configure Interface Descriptions

<p align="center">
<a href="PASTE-IMAGE-LINK-HERE">
<img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2009%20Lab%20-%20Interface%20Configuration-4.1.png" width="1200">
  <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2009%20Lab%20-%20Interface%20Configuration-4.2.png" width="1200">
</a>
</p>

Interface descriptions were added to document physical connections.

Example

```Cisco
description R1

description SW2

description PC1

description PC2
```

Proper interface descriptions significantly improve troubleshooting and operational efficiency in production networks.


---

# Step 8 — Disable Unused Interfaces

<p align="center">
<a href="PASTE-IMAGE-LINK-HERE">
<img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2009%20Lab%20-%20Interface%20Configuration-5.1.png" width="1200">
  <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2009%20Lab%20-%20Interface%20Configuration-5.2.png" width="1200">
</a>
</p>

Unused switch interfaces were administratively disabled.

This is considered a networking best practice because it:

- Improves security
- Prevents unauthorized connections
- Reduces accidental misconfigurations

---

# Commands Practiced

```Cisco
hostname

interface

ip address

no shutdown

speed

duplex

description

show interfaces status

show ip interface brief

shutdown
```

---

# Skills Practiced

- Cisco IOS Navigation
- Interface Configuration
- Interface Documentation
- Duplex Configuration
- Speed Configuration
- Interface Verification
- Layer 1 Troubleshooting
- Layer 2 Administration
- Enterprise Network Documentation
- Device Management

---

# What I Learned

Today's lab introduced the importance of interface management beyond simple IP addressing.

I learned that configuring interface speed, duplex, descriptions, and administratively disabling unused ports are all considered standard operational practices in enterprise environments.

Although these tasks may seem simple, they greatly improve network reliability, troubleshooting efficiency, and security.

This lab reinforced that successful network engineering isn't just about getting devices online—it's also about properly documenting and managing every interface.

---

# Lab Status

✅ Day 09 Complete

### Topics Covered

- Interface Configuration
- Speed & Duplex
- Interface Descriptions
- Hostname Configuration
- Device Management
- Cisco IOS
- Interface Verification
- Security Best Practices

---

## Next Lab

➡️ Ethernet LAN Switching Fundamentals
