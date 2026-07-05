# Day 16 - VLANs Part 1: Configuration and Trunking

## Overview

Today's CCNA lab focused on **VLANs**, or Virtual Local Area Networks.

The objective was to create and assign VLANs on a single switch, verify the VLAN database, configure trunk links, and test inter-VLAN communication behavior.

This lab combined switch VLAN configuration, access port assignment, trunk configuration, router-on-a-stick sub-interfaces, default gateway setup, and broadcast domain verification.

---

## Network Topology

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2016%20Lab%20-%20VLANs%20(Part%201).png" alt="Day 16 VLAN Network Topology">
  </a>
</p>

---

## Lab Objectives

* Create VLANs on SW1
* Name VLANs: Engineering, HR, Sales
* Assign access ports to VLANs
* Configure trunk link between SW1 and R1
* Configure router-on-a-stick sub-interfaces
* Assign IP addresses to router sub-interfaces
* Configure PC default gateways
* Verify VLAN database
* Test inter-VLAN connectivity
* Verify broadcast domain behavior with ping tests

---

## VLAN Requirements

| VLAN | Name       | Subnet          | Gateway        | Hosts           |
| ---- | ---------- | --------------- | -------------- | --------------- |
| 10   | Engineering | 10.0.0.0/26     | 10.0.0.62      | PC1, PC2        |
| 20   | HR         | 10.0.0.64/26    | 10.0.0.126     | PC3, PC4        |
| 30   | Sales      | 10.0.0.128/26   | 10.0.0.190     | PC5, PC6        |

---

## Step 1 - Configure VLANs on SW1

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2016%20Lab%20-%20VLANs%20(Part%201)-1.1.png" alt="VLAN Configuration">
  </a>
</p>

VLANs were created and named on SW1 using VLAN database configuration mode.

Commands used included:

```cisco
vlan 10
 name Engineering
vlan 20
 name HR
vlan 30
 name Sales
```

---

## Step 2 - Configure Access Ports on SW1

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2016%20Lab%20-%20VLANs%20(Part%201)-1.2.png" alt="Access Port Configuration">
  </a>
</p>

Switch ports connecting to PCs were configured as access ports and assigned to their respective VLANs.

```cisco
interface fastEthernet 3/1
 switchport mode access
 switchport access vlan 10

interface fastEthernet 4/1
 switchport mode access
 switchport access vlan 20

interface fastEthernet 8/1
 switchport mode access
 switchport access vlan 30
```

---

## Step 3 - Configure Trunk Port on SW1

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2016%20Lab%20-%20VLANs%20(Part%201)-1.3.png" alt="Trunk Port Configuration">
  </a>
</p>

The uplink port to the router was configured as a trunk to carry multiple VLANs.

```cisco
interface gigabitEthernet 0/1
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30
```

---

## Step 4 - Configure Router-on-a-Stick Sub-interfaces

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2016%20Lab%20-%20VLANs%20(Part%201)-1.4.png" alt="Router Sub-interfaces">
  </a>
</p>

R1 was configured with sub-interfaces for each VLAN using 802.1q encapsulation.

```cisco
interface gigabitEthernet 0/0.10
 encapsulation dot1Q 10
 ip address 10.0.0.62 255.255.255.192

interface gigabitEthernet 0/1.20
 encapsulation dot1Q 20
 ip address 10.0.0.126 255.255.255.192

interface gigabitEthernet 2/2.30
 encapsulation dot1Q 30
 ip address 10.0.0.190 255.255.255.192
```

---

## Step 5 - Configure PC IP Addresses and Gateways

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2016%20Lab%20-%20VLANs%20(Part%201)-1.5.png" alt="PC IP Configuration">
  </a>
</p>

Each PC was assigned an IP address, subnet mask, and default gateway.

| PC    | VLAN    | IP Address    | Subnet Mask      | Default Gateway |
| ----- | ------- | ------------- | ---------------- | --------------- |
| PC1   | VLAN 10 | 10.0.0.1      | 255.255.255.192  | 10.0.0.62       |
| PC2   | VLAN 10 | 10.0.0.2      | 255.255.255.192  | 10.0.0.62       |
| PC3   | VLAN 20 | 10.0.0.65     | 255.255.255.192  | 10.0.0.126      |
| PC4   | VLAN 20 | 10.0.0.66     | 255.255.255.192  | 10.0.0.126      |
| PC5   | VLAN 30 | 10.0.0.129    | 255.255.255.192  | 10.0.0.190      |
| PC6   | VLAN 30 | 10.0.0.130    | 255.255.255.192  | 10.0.0.190      |

---

## Step 6 - Verify VLAN Database

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2016%20Lab%20-%20VLANs%20(Part%201)-2.png" alt="VLAN Database Verification">
  </a>
</p>

VLAN membership was verified using:

```cisco
show vlan brief
```

---

## Step 7 - Verify Inter-VLAN Connectivity

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2016%20Lab%20-%20VLANs%20(Part%201)-3.png" alt="Inter-VLAN Ping Test">
  </a>
</p>

Ping tests confirmed successful communication between VLANs via the router.

---

## Step 8 - Verify Broadcast Domain Behavior

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2016%20Lab%20-%20VLANs%20(Part%201)-4.1.png" alt="Broadcast Domain Verification">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2016%20Lab%20-%20VLANs%20(Part%201)-4.2.png" alt="Broadcast Ping Results">
  </a>
</p>

A broadcast ping to 10.0.0.63 confirmed that broadcast traffic stays within the local VLAN unless routed.

Results showed TTL values:
- TTL=255: Cisco router/gateway
- TTL=128: Windows PC/host

---

## Step 9 - Verify Trunk Configuration

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2016%20Lab%20-%20VLANs%20(Part%201)-4.2.png" alt="Trunk Verification">
  </a>
</p>

Trunk status was verified using:

```cisco
show interfaces trunk
```

---

## Commands Practiced

```cisco
vlan database
vlan <id>
 name <name>

interface <type> <number>
 switchport mode access
 switchport access vlan <id>

interface <type> <number>
 switchport mode trunk
 switchport trunk allowed vlan <list>

interface <type> <number>.<subif>
 encapsulation dot1Q <vlan-id>
 ip address <address> <mask>

show vlan brief
show interfaces trunk
show ip interface brief
ping
ipconfig
```

---

## Skills Practiced

* VLAN creation and naming
* Access port assignment
* Trunk configuration
* Router-on-a-stick sub-interfaces
* 802.1q encapsulation
* Default gateway configuration
* Broadcast domain verification
* Ping and connectivity testing
* TTL analysis

---

## What I Learned

This lab helped me understand how VLANs segment broadcast domains and how trunk links carry multiple VLANs between switches and routers.

The key insight was that a broadcast ping stays within the local VLAN unless the router routes it. Before configuring trunks and sub-interfaces, connectivity was 100% loss. After proper VLAN, access port, trunk, and router configuration, full inter-VLAN connectivity was achieved.

The router-on-a-stick configuration uses sub-interfaces with `encapsulation dot1Q` to tag traffic for each VLAN, allowing a single physical router interface to route between multiple VLANs.

---

## Lab Status

✅ Day 16 Complete

### Topics Covered

* VLANs
* Access Ports
* Trunking
* 802.1q Encapsulation
* Router-on-a-Stick
* Inter-VLAN Routing
* Broadcast Domains

---

**Repository:** [Network-Engineering-Labs-CCNA-2026](https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026)
