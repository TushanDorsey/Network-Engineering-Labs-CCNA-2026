# Day 16 - VLANs Part 1: Configuration and Inter-VLAN Routing

## Overview

Today's CCNA lab focused on **VLANs**, or Virtual Local Area Networks.

The objective was to segment devices into three VLANs, assign gateways using the last usable address of each subnet, configure router interfaces for inter-VLAN routing, and verify connectivity with ping and broadcast tests.

This lab combined PC IP configuration, router interface configuration, switch VLAN creation, port assignment, inter-VLAN routing verification, and broadcast domain analysis.

---

## Network Topology

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2016%20Lab%20-%20VLANs%20(Part%201).png" alt="Day 16 VLAN Network Topology">
  </a>
</p>

---

## Lab Objectives

* Configure PCs with IP addresses, subnet masks, and default gateways
* Set gateways as the **last usable address** of each VLAN subnet
* Connect R1 to SW1 with three physical links
* Configure one router interface per VLAN
* Create and name VLANs on SW1
* Assign switch ports to proper VLANs
* Verify connectivity with ping and broadcast tests

---

## VLAN Requirements

| VLAN    | Name       | Subnet          | Gateway      | Hosts          |
| ------- | ---------- | --------------- | ------------ | -------------- |
| VLAN 10 | Engineering | 10.0.0.0/26     | 10.0.0.62    | PC1, PC2       |
| VLAN 20 | HR         | 10.0.0.64/26    | 10.0.0.126   | PC3, PC4       |
| VLAN 30 | Sales      | 10.0.0.128/26   | 10.0.0.190   | PC5, PC6       |

---

## Step 1 - Configure PCs with IP Addresses and Gateways

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2016%20Lab%20-%20VLANs%20(Part%201)-1.1.png" alt="PC1, PC2, and PC3 Configuration">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2016%20Lab%20-%20VLANs%20(Part%201)-1.2.png" alt="PC2 IP Configuration Verification">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2016%20Lab%20-%20VLANs%20(Part%201)-1.3.png" alt="Topology with Lab Instructions">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2016%20Lab%20-%20VLANs%20(Part%201)-1.5.png" alt="PC5 IP Configuration">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2016%20Lab%20-%20VLANs%20(Part%201)-1.6.png" alt="PC6 IP Configuration">
  </a>
</p>

Each PC received its VLAN IP, mask 255.255.255.192, and a default gateway set to the **last usable address** of its subnet.

Assignments:
- PC1: 10.0.0.1, gateway 10.0.0.62
- PC2: 10.0.0.2, gateway 10.0.0.62
- PC3: 10.0.0.65, gateway 10.0.0.126
- PC4: 10.0.0.66, gateway 10.0.0.126
- PC5: 10.0.0.129, gateway 10.0.0.190
- PC6: 10.0.0.130, gateway 10.0.0.190

The -1.1 screenshot shows the initial PC configuration. The later -1.x screenshots verify each PC's completed IP addressing and show the lab topology/instructions.

---

## Step 2 - Configure R1 Interfaces

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2016%20Lab%20-%20VLANs%20(Part%201)-2.png" alt="R1 Interface Configuration">
  </a>
</p>

R1 was configured with one physical interface per VLAN to provide inter-VLAN routing.

```cisco
interface gigabitEthernet 0/0
 ip address 10.0.0.62 255.255.255.192
 no shutdown

interface gigabitEthernet 0/1
 ip address 10.0.0.126 255.255.255.192
 no shutdown

interface gigabitEthernet 0/2
 ip address 10.0.0.190 255.255.255.192
 no shutdown
```

All three interfaces reached `up/up` after configuration.

---

## Step 3 - Configure SW1 VLANs and Ports

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2016%20Lab%20-%20VLANs%20(Part%201)-3.png" alt="SW1 VLAN Database">
  </a>
</p>

VLANs were created and named on SW1.

```cisco
vlan 10
 name Engineering

vlan 20
 name HR

vlan 30
 name Sales
```

Ports were assigned to their VLANs in access mode.

---

## Step 4 - Verify Connectivity and Broadcast Behavior

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2016%20Lab%20-%20VLANs%20(Part%201)-4.1.png" alt="Ping Tests">
  </a>
</p>

Inter-VLAN pings succeeded. One ping to 10.0.0.189 timed out, while pings to 10.0.0.130 and 10.0.0.65 succeeded.

---

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2016%20Lab%20-%20VLANs%20(Part%201)-4.2.png" alt="Broadcast Domain Verification">
  </a>
</p>

Broadcast traffic stayed inside VLAN 10 and was not forwarded into VLAN 20 or VLAN 30. This confirms each VLAN is a separate broadcast domain.

---

## Commands Practiced

```cisco
interface gigabitEthernet <number>
 ip address <address> <mask>
 no shutdown

vlan <id>
 name <name>

interface <type> <number>
 switchport mode access
 switchport access vlan <id>

show vlan brief
show ip interface brief
ping
ipconfig
```

---

## Skills Practiced

* VLAN creation and naming
* Router interface configuration for each VLAN
* Switch port VLAN assignment
* PC IP and gateway configuration
* Inter-VLAN connectivity verification
* Broadcast domain behavior analysis
* TTL interpretation

---

## What I Learned

This lab showed that VLANs create separate broadcast domains and a router can provide inter-VLAN routing using one physical interface per VLAN, without trunking.

Key observations:
- Gateway = last usable address of the subnet
- Each VLAN needs its own router interface on the same subnet
- Router forwards unicast between VLANs
- Router does not forward broadcast between VLANs

Before configuring router interfaces, pings between VLANs failed. After adding the three router interfaces and assigning gateways, inter-VLAN communication was successful and broadcast behavior was constrained to the local VLAN.

---

## Lab Status

✅ Day 16 Complete

### Topics Covered

* VLANs
* Access Ports
* Inter-VLAN Routing via Physical Interfaces
* Broadcast Domains
* Gateway Configuration
* Connectivity Verification
* PC IP Configuration

---

**Repository:** [Network-Engineering-Labs-CCNA-2026](https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026)
