# Day 15 - VLSM & Static Routing

## Overview

Today's CCNA lab focused on **VLSM**, or Variable Length Subnet Masking.

The objective was to take the `192.168.5.0/24` network and subnet it efficiently based on the number of hosts required for each LAN.

This lab combined subnetting, router interface configuration, endpoint addressing, point-to-point addressing, static routing, and connectivity testing.

---

## Network Topology

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-15-Lab-VLSM.png" alt="Day 15 VLSM Network Topology">
  </a>
</p>

---

## Lab Objectives

* Subnet `192.168.5.0/24` using VLSM
* Provide enough addresses for each LAN
* Configure router interfaces
* Configure PC IP addresses
* Assign first usable IPs to PCs
* Assign last usable IPs to router interfaces
* Configure the point-to-point link between R1 and R2
* Configure static routes
* Verify routing tables
* Test end-to-end connectivity

---

## VLSM Requirements

| Network        | Host Requirement | Purpose       |
| -------------- | ---------------: | ------------- |
| LAN 1          |         45 Hosts | PC1 Network   |
| LAN 2          |         64 Hosts | PC2 Network   |
| LAN 3          |         14 Hosts | PC3 Network   |
| LAN 4          |          9 Hosts | PC4 Network   |
| Point-to-Point |          2 Hosts | R1 to R2 Link |

---

## Step 1 - VLSM Planning

The first step was determining the correct subnet sizes based on host requirements.

The largest networks were assigned first to avoid wasting address space.

---

## Step 2 - Configure R1 Interfaces

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-15-Lab-VLSM-1.1.png" alt="R1 Interface Configuration">
  </a>
</p>

R1 was configured with LAN gateway addresses and the point-to-point link toward R2.

Commands used included:

```cisco
interface g0/0
ip address
no shutdown

interface g0/1
ip address
no shutdown

interface g0/0/0
ip address
no shutdown
```

---

## Step 3 - Configure R2 Interfaces

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-15-Lab-VLSM-1.2.png" alt="R2 Interface Configuration">
  </a>
</p>

R2 was configured with its LAN gateway addresses and the point-to-point link back to R1.

---

## Step 4 - Configure PC Addressing

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-15-Lab-VLSM-1.5.png" alt="PC Addressing">
     <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-15-Lab-VLSM-1.6.png" alt="PC Addressing">
     <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-15-Lab-VLSM-1.7.png" alt="PC Addressing">
     <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-15-Lab-VLSM-1.8.png" alt="PC Addressing">
  </a>
</p>

Each PC was configured with:

* First usable IP address in its subnet
* Correct subnet mask
* Default gateway pointing to the router interface

---

## Step 5 - Verify Local Gateway Connectivity

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-15-Lab-VLSM-1.6.png" alt="Gateway Ping Test">
  </a>
</p>

Each PC successfully pinged its default gateway, confirming local LAN connectivity.

---

## Step 6 - Configure Static Routes

<p align="center">
   <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-15-Lab-VLSM-2.1.png" alt="PC Addressing">
   <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-15-Lab-VLSM-2.2.png" alt="PC Addressing">
    <img src="" alt="Static Route Configuration">
  </a>
</p>

Static routes were configured so each router knew how to reach the remote LANs.

Example command:

```cisco
ip route <destination-network> <subnet-mask> <next-hop>
```

---

## Step 7 - Verify Routing Tables

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
     <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-15-Lab-VLSM-2.2.png" alt="PC Addressing">
  </a>
</p>

Routing tables were verified using:

```cisco
show ip route
```

This confirmed connected, local, and static routes were present.

---

## Step 8 - End-to-End Connectivity Testing

<p align="center">
 <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-15-Lab-VLSM-3.png" alt="PC Addressing">
    <img src="PASTE-IMAGE-LINK-HERE" alt="End-to-End Ping Test">
  </a>
</p>

Final ping tests confirmed successful communication between devices across different VLSM subnets.

---

## Commands Practiced

```cisco
show ip interface brief
show ip route
interface
ip address
no shutdown
ip route
do write
ping
ipconfig
```

---

## Skills Practiced

* VLSM
* Subnet Planning
* IPv4 Addressing
* Static Routing
* Router Interface Configuration
* Default Gateway Configuration
* Routing Table Verification
* End-to-End Connectivity Testing
* Cisco IOS Troubleshooting

---

## What I Learned

This lab helped me understand how VLSM allows networks to use IP address space more efficiently.

Instead of giving every LAN the same subnet size, VLSM allows each network to receive only the amount of address space it actually needs.

This is important in real enterprise networks because proper subnet planning helps reduce wasted IP addresses and keeps network designs organized.

This lab also reinforced how subnetting, routing, gateway configuration, and static routes all work together to allow communication across multiple networks.

---

## Lab Status

✅ Day 15 Complete

### Topics Covered

* VLSM
* IPv4 Subnetting
* Static Routing
* Router Interfaces
* Gateway Configuration
* Connectivity Testing
