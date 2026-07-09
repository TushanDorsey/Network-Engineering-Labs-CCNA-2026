# Day 19 - VTP, Trunking, and VLAN Management

## Overview

Today's CCNA lab focused on **VTP (VLAN Trunking Protocol)**, trunk port configuration, DTP negotiation, and VLAN propagation across multiple switches.

The objective was to configure a three-switch topology with centralized VLAN management using VTP, while understanding the differences between VTP modes: Server, Transparent, and Client.

This lab is important because VTP simplifies VLAN administration in campus networks, but its behavior changes dramatically based on mode. Misunderstanding VTP can cause VLAN propagation issues or security gaps.

---

## Network Topology

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-19-Lab-DTP-%26-VTP.png">
  </a>
</p>

---

## Lab Objectives

* Configure switch-to-switch ports as trunks
* Disable DTP negotiation on all trunk ports
* Verify trunk operational status with `show interface switchport`
* Configure VTP domain and mode on each switch
* Create VLANs on VTP Server and verify propagation
* Test VTP Transparent mode behavior
* Test VTP Client mode restrictions
* Configure host-facing access ports and verify VLAN assignment

---

## VTP Domain Requirements

| Switch | VTP Mode | Domain | VLANs Created |
| ------ | -------- | ------ | ------------- |
| SW1    | Server   | CCNA   | 10, 20, 30     |
| SW2    | Transparent | CCNA | 40            |
| SW3    | Client   | CCNA   | Cannot create local VLANs |

---

## Step 1 - Configure Trunk Ports and Disable DTP

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-19-Lab-DTP-%26-VTP-1.1.png">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-19-Lab-DTP-%26-VTP-1.2.png">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-19-Lab-DTP-%26-VTP-1.3.png">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-19-Lab-DTP-%26-VTP-1.4.png">
  </a>
</p>

Configure all inter-switch connections as trunks and disable DTP negotiation.

```cisco
interface gigabitEthernet0/1
 switchport mode trunk
 switchport nonegotiate

interface gigabitEthernet0/2
 switchport mode trunk
 switchport nonegotiate
```

**Verification:**

```cisco
show interface gigabitEthernet0/1 switchport
show interface gigabitEthernet0/2 switchport
```

Expected output indicators:
- Administrative Mode: trunk
- Operational Mode: trunk
- Negotiation of Trunking: Off
- Trunking Encapsulation: dot1q

---

## Step 2 - Configure VTP Domain on SW1

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-19-Lab-DTP-%26-VTP-2.1.png">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-19-Lab-DTP-%26-VTP-2.2.png">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-19-Lab-DTP-%26-VTP-2.3.png">
  </a>
</p>

Set SW1 as the VTP Server and create VLANs 10, 20, and 30.

```cisco
vtp domain CCNA
vlan 10
 name VLAN10
vlan 20
 name VLAN20
vlan 30
 name VLAN30
```

**Verification:**

```cisco
show vtp status
show vlan brief
```

Expected VTP status:
- VTP Operating Mode: Server
- VTP Domain Name: CCNA
- Configuration Revision increments with each change

Expected VLAN brief:
- VLAN 10, 20, 30 listed as active
- SW2 and SW3 should show these VLANs in their databases

---

## Step 3 - Configure SW2 in VTP Transparent Mode

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-19-Lab-DTP-%26-VTP-3.1.png">
  </a>
</p>

Set SW2 to VTP Transparent mode and add VLAN40.

```cisco
vtp mode transparent
vlan 40
 name VLAN40
```

**Verification:**

```cisco
show vtp status
show vlan brief
```

Key finding:
- SW2 shows VLAN40 as active
- **SW1 and SW3 do NOT show VLAN40** — Transparent mode does not propagate VLANs via VTP
- This is expected behavior, not a misconfiguration

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-19-Lab-DTP-%26-VTP-3.2.png">
     <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-19-Lab-DTP-%26-VTP-3.3.png">
  </a>
</p>
---

## Step 4 - Configure SW3 in VTP Client Mode

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-19-Lab-DTP-%26-VTP-4.1.png">
  </a>
</p>

Set SW3 to VTP Client mode and attempt to create VLAN50.

```cisco
vtp mode client
vlan 50
```

**Result:**
```
SW3 VLAN configuration not allowed when device is in CLIENT mode.
```

**Verification:**

```cisco
show vtp status
show vlan brief
```

Key finding:
- VLAN50 is **not created** — Client mode cannot modify the VLAN database locally
- SW3 receives VLANs from the Server (SW1) but cannot create or modify them
- This is expected VTP Client behavior

---

## Step 5 - Configure Access Ports and Verify DTP

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-19-Lab-DTP-%26-VTP-5.1.png">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-19-Lab-DTP-%26-VTP-5.2.png">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-19-Lab-DTP-%26-VTP-5.3.png">
  </a>
</p>

Configure all host-facing ports as access ports in the correct VLAN.

```cisco
! SW1 host ports
interface range fastEthernet0/1 - 2
 switchport mode access
 switchport access vlan 10

interface fastEthernet0/3
 switchport mode access
 switchport access vlan 20

! SW2 host ports
interface fastEthernet0/3 - 4
 switchport mode access
 switchport access vlan 20

! SW3 host ports
interface range fastEthernet0/1 - 2
 switchport mode access
 switchport access vlan 30
```

**Verification:**
<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-19-Lab-DTP-%26-VTP-5.4.png">
   
  </a>
</p>
```cisco
show vlan brief
show interface fastEthernet0/1 switchport
```

Expected:
- VLAN 10: SW1 Fa0/1-2
- VLAN 20: SW2 Fa0/3-4, SW1 Fa0/3
- VLAN 30: SW3 Fa0/1-4
- All host ports show Administrative Mode: static access
- Negotiation of Trunking: Off

---

## Commands Practiced

```cisco
! Trunk configuration
interface <type> <number>
 switchport mode trunk
 switchport nonegotiate

! VTP configuration
vtp domain <domain-name>
vtp mode {server | client | transparent}

! VLAN creation
vlan <id>
 name <name>

! Verification
show vtp status
show vlan brief
show interface <type> <number> switchport
```

---

## Skills Practiced

* Trunk port configuration and verification
* DTP disable with `switchport nonegotiate`
* VTP Server, Transparent, and Client mode behavior
* VLAN database propagation rules
* Access port VLAN assignment
* Switchport mode verification via CLI
* Understanding VTP domain boundaries

---

## What I Learned

VTP is powerful but dangerous if misunderstood. The Server creates and propagates VLANs, Transparent mode keeps VLANs local, and Client mode receives VLANs but cannot create them.

The key security takeaway: never leave a switch in VTP Server mode without a domain password. A rogue switch can overwrite your entire VLAN database if domain/revision numbers align.

DTP should be disabled on all trunk ports. Leaving it on means the port could negotiate down to access mode if the other side is misconfigured, silently breaking VLAN traffic.

Access ports should never negotiate trunks. Manual `switchport mode access` plus DTP off guarantees the port stays in access mode regardless of what connects to it.

---

## Lab Status

✅ Day 19 Complete

### Topics Covered

* VTP Server, Transparent, and Client modes
* Trunk port configuration
* DTP disable
* VLAN propagation and domain boundaries
* Access port hardening
* Switchport verification commands

---

**Repository:** [Network-Engineering-Labs-CCNA-2026](https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026)
