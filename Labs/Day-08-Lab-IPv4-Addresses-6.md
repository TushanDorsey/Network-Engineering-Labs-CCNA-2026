# Day 08 - IPv4 Address Configuration & Router Interface Setup

## Overview

Today's CCNA lab focused on configuring IPv4 addressing on Cisco routers and end devices while verifying end-to-end connectivity between multiple networks.

Unlike previous labs that concentrated on Layer 2 switching, this exercise introduced Layer 3 routing by assigning IP addresses directly to router interfaces, configuring default gateways for hosts, and verifying communication across three separate LANs.

By the end of this lab every network successfully communicated through a single Cisco router acting as the gateway between all three subnets.

---

## Network Topology

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2008%20Lab%20-%20IPv4%20Addresses.png" width="1200">
  </a>
</p>

<p align="center">
Initial topology showing one Cisco 2911 router connecting three separate IPv4 networks.
</p>

---

## Lab Objectives

Throughout this lab I completed the following objectives:

- Configure the router hostname
- Examine router interfaces
- Configure IPv4 addresses on router interfaces
- Bring interfaces online using **no shutdown**
- Configure interface descriptions
- Verify interface status
- Configure IPv4 addresses on end devices
- Configure default gateways
- Save the running configuration
- Test end-to-end connectivity using ICMP Ping

---

# Network Design

This lab consists of three independent LANs connected through a Cisco 2911 Router.

## Network A

```
15.0.0.0/8
```

Devices

- PC1
- SW1
- Router G0/0

Gateway

```
15.255.255.254
```

---

## Network B

```
182.98.0.0/16
```

Devices

- PC2
- SW2
- Router G0/1

Gateway

```
182.98.255.254
```

---

## Network C

```
201.191.20.0/24
```

Devices

- PC3
- SW3
- Router G0/2

Gateway

```
201.191.20.254
```

---

# Step 1 — Configure the Router Hostname

<p align="center">
<a href="PASTE-IMAGE-LINK-HERE">
<img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2008%20Lab%20-%20IPv4%20Addresses-1.png" width="1200">
</a>
</p>

The first task was placing the router into Global Configuration Mode and assigning the hostname.

Commands Used

```Cisco
enable
configure terminal
hostname R1
```

Changing the hostname makes device identification significantly easier during larger network deployments.

---

# Step 2 — Verify Router Interfaces

<p align="center">
<a href="PASTE-IMAGE-LINK-HERE">
<img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2008%20Lab%20-%20IPv4%20Addresses-2.png" width="1200">
</a>
</p>

Before configuring interfaces, I verified their current status using:

```Cisco
do show ip interface brief
```

At this point all interfaces were:

- Unassigned
- Administratively Down

This establishes the router's baseline before configuration.

---

# Step 3 — Configure Router Interface IP Addresses

<p align="center">
<a href="PASTE-IMAGE-LINK-HERE">
<img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2008%20Lab%20-%20IPv4%20Addresses-3.png" width="1200">
</a>
</p>

Each GigabitEthernet interface was configured with the correct IPv4 address.

Configured Interfaces

### GigabitEthernet0/0

```Cisco
ip address 15.255.255.254 255.0.0.0
no shutdown
```

---

### GigabitEthernet0/1

```Cisco
ip address 182.98.255.254 255.255.0.0
no shutdown
```

---

### GigabitEthernet0/2

```Cisco
ip address 201.191.20.254 255.255.255.0
no shutdown
```

After enabling each interface the router immediately reported that the interfaces transitioned to an **UP** state.

---

# Step 4 — Verify Interface Status

<p align="center">
<a href="PASTE-IMAGE-LINK-HERE">
<img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2008%20Lab%20-%20IPv4%20Addresses-4.png" width="1200">
</a>
</p>

After configuration I verified all interfaces again.

Command

```Cisco
show ip interface brief
```

Result

All configured interfaces displayed:

```
Status: UP

Protocol: UP
```

confirming proper Layer 1 and Layer 2 connectivity.

---

# Step 5 — Save the Configuration

<p align="center">
<a href="PASTE-IMAGE-LINK-HERE">
<img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2008%20Lab%20-%20IPv4%20Addresses-5.png" width="1200">
</a>
</p>

After verifying the configuration I saved it to NVRAM.

Command

```Cisco
copy running-config startup-config
```

This ensures the router retains its configuration after a reboot.

---

# Step 6 — Configure End Device IPv4 Addresses

## PC1

<p align="center">
<a href="PASTE-IMAGE-LINK-HERE">
<img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2008%20Lab%20-%20IPv4%20Addresses-6.1.png" width="1200">
</a>
</p>

Configured

```
IP Address

15.0.0.1

Subnet Mask

255.0.0.0

Gateway

15.255.255.254
```

Connectivity to its default gateway was successfully verified.

---

## PC2

<p align="center">
<a href="PASTE-IMAGE-LINK-HERE">
<img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2008%20Lab%20-%20IPv4%20Addresses-6.2.png" width="1200">
</a>
</p>

Configured

```
IP Address

182.98.0.1

Subnet Mask

255.255.0.0

Gateway

182.98.255.254
```

Gateway connectivity was confirmed using ICMP Ping.

---

## PC3

<p align="center">
<a href="PASTE-IMAGE-LINK-HERE">
<img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2008%20Lab%20-%20IPv4%20Addresses-6.3.png" width="1200">
</a>
</p>

Configured

```
IP Address

201.191.20.1

Subnet Mask

255.255.255.0

Gateway

201.191.20.254
```

Connectivity to the router was verified successfully.

---

# Step 7 — End-to-End Connectivity Testing

## PC1 → PC2

<p align="center">
<a href="PASTE-IMAGE-LINK-HERE">
<img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2008%20Lab%20-%20IPv4%20Addresses-7.1.png" width="1200">
</a>
</p>

PC1 successfully communicated with PC2 across different subnets.

---

## PC1 → PC3

<p align="center">
<a href="PASTE-IMAGE-LINK-HERE">
<img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2008%20Lab%20-%20IPv4%20Addresses-7.2.png" width="1200">
</a>
</p>

PC1 successfully communicated with PC3, confirming routing functionality across all three networks.

---

# Commands Practiced

```Cisco
enable

configure terminal

hostname

show ip interface brief

interface g0/x

ip address

no shutdown

copy running-config startup-config

ping
```

---

# Skills Practiced

- IPv4 Address Configuration
- Router Interface Configuration
- Default Gateway Configuration
- Interface Verification
- Cisco IOS Navigation
- Layer 3 Routing Fundamentals
- ICMP Connectivity Testing
- Configuration Saving
- Cisco CLI Navigation

---

# What I Learned

Today's lab introduced one of the most important concepts in networking—routing between different IPv4 networks.

I learned how routers serve as the default gateway for devices, allowing communication between otherwise isolated LANs.

Configuring interfaces manually reinforced how IP addressing, subnet masks, and gateways all work together to enable successful communication.

By the end of the lab every network was successfully communicating through Router R1, demonstrating the foundation of Layer 3 networking.

---

# Lab Status

✅ Day 08 Complete

### Topics Covered

- IPv4 Addressing
- Router Configuration
- Interface Configuration
- Default Gateways
- Cisco CLI
- ICMP
- End-to-End Connectivity

---

### Next Topic

➡️ Static Routing / IPv4 Routing Fundamentals
