# Day 18 - Multilayer Switching: SVIs and Inter-VLAN Routing

## Overview

Today's CCNA lab focused on **multilayer switching**, specifically replacing ROAS with a point-to-point Layer 3 connection and configuring SVIs on a Layer 3 switch for inter-VLAN routing.

The objective was to:
1. Replace Router-on-a-Stick with a true Layer 3 point-to-point link and add a default route on the multilayer switch
2. Configure SVIs for each VLAN using the last usable IP address
3. Test inter-VLAN connectivity
4. Test internet connectivity

This lab is important because modern enterprise networks use Layer 3 switches for inter-VLAN routing to reduce latency, simplify cabling, and consolidate switching and routing in one device.

---

## Network Topology
<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-18-Lab-Multilayer%20Switching.pkt.png">
  </a>
</p>

| Device | Model | Role |
|--------|-------|------|
| R1 | 2911 | Internet edge router |
| SW1 | 2960-24TT | Access/distribution switch |
| SW2 | 3650-24PS | Multilayer switch (replaced from Day 17) |
| PC1-PC7 | PC-PT | End hosts in VLAN10/20/30 |

**VLAN Plan:**
| VLAN | Subnet | Gateway (last usable) |
|------|--------|----------------------|
| VLAN10 | 10.0.0.0/26 | 10.0.0.62 |
| VLAN20 | 10.0.0.64/26 | 10.0.0.126 |
| VLAN30 | 10.0.0.128/26 | 10.0.0.190 |

**Links:**
- R1 G0/0 → Internet cloud (public path)
- R1 G1/0/2 → SW2 G1/0/1 via 10.0.0.192/30 point-to-point
- SW1 ↔ SW2 via trunk

---

## Step 1: Replace ROAS with Point-to-Point Layer 3

### 1.1 Router Side — Remove Subinterfaces, Configure Physical Interface

<p align="center">
  <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-18-Lab-Multilayer%20Switching-1.1.pkt.png">
</p>

Removed ROAS subinterfaces and reconfigured physical interface:

```cisco
R1(config)# interface g0/0
R1(config-if)# ip address 10.0.0.194 255.255.255.252
```

### 1.2 Switch Side — Convert Port to Layer 3

<p align="center">
  <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-18-Lab-Multilayer%20Switching-1.2.pkt.png" alt="SW2 G1/0/2 no switchport and IP assignment">
</p>

```cisco
SW2(config)# interface g1/0/2
SW2(config-if)# no switchport
SW2(config-if)# ip address 10.0.0.193 255.255.255.252
SW2(config-if)# no shutdown
```

### 1.3 Default Route on SW2

<p align="center">
  <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-18-Lab-Multilayer%20Switching-1.3.pkt.png">
</p>

```cisco
SW2(config)# ip route 0.0.0.0 0.0.0.0 10.0.0.194
```

**Verification:**

<p align="center">
  <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-18-Lab-Multilayer%20Switching-1.3.pkt.png">
</p>

```cisco
SW2# show ip route

Gateway of last resort is 10.0.0.194 to network 0.0.0.0

C    10.0.0.192/30 is directly connected, GigabitEthernet1/0/2
S*   0.0.0.0/0 [1/0] via 10.0.0.194
```

---

## Step 2: Configure SVIs on SW2

<p align="center">
  <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-18-Lab-Multilayer%20Switching-2.1.pkt.png">
</p>

```cisco
SW2(config)# interface vlan 10
SW2(config-if)# ip address 10.0.0.62 255.255.255.192
SW2(config-if)# no shutdown

SW2(config)# interface vlan 20
SW2(config-if)# ip address 10.0.0.126 255.255.255.192
SW2(config-if)# no shutdown

SW2(config)# interface vlan 30
SW2(config-if)# ip address 10.0.0.190 255.255.255.192
SW2(config-if)# no shutdown
```

---

## Step 3: Test Inter-VLAN Connectivity

<p align="center">
  <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-18-Lab-Multilayer%20Switching-3.1.pkt.png">
</p>

```cisco
PC1> ping 10.0.0.4

Reply from 10.0.0.4: bytes=32 time<1ms TTL=128
Packets: Sent = 4, Received = 4, Lost = 0 (0% loss)
```

---

## Step 4: Test Internet Connectivity

<p align="center">
  <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-18-Lab-Multilayer%20Switching-4.1.pkt.png">
</p>

```cisco
PC1> ping 1.1.1.1

Request timed out.
Reply from 1.1.1.1: bytes=32 time=2ms TTL=253
Packets: Sent = 4, Received = 4, Lost = 0 (0% loss)
```

---

## Lab Status

✅ Day 18 Complete

### Topics Covered
* Replace ROAS with point-to-point Layer 3
* Default route configuration on multilayer switch
* SVI Configuration
* Layer 3 Routing Enablement
* Gateway Configuration
* Inter-VLAN Connectivity
* Internet Connectivity
* Routing Table Verification

---

**Repository:** [Network-Engineering-Labs-CCNA-2026](https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026)
