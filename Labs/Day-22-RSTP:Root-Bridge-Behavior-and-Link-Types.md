# Day 22 - RSTP: Root Bridge Behavior and Link Types

## Overview

Today's CCNA lab focused on **Rapid Spanning Tree Protocol (RSTP)** behavior in a multi-switch topology with shared segments. The goal wasn't just to identify port roles — it was to understand why the root bridge behaves differently when hubs enter the picture, and how to classify link types correctly for RSTP operation.

This lab matters because RSTP is the default on modern Cisco switches. If you only understand classic STP port roles, you'll misdiagnose RSTP topologies in production.

---

## Network Topology

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-22-Lab-Rapid-STP.png">
  </a>
</p>

---

## Switch Inventory

| Device | Model | Priority | MAC Address | Role |
|--------|-------|----------|-------------|------|
| SW1 | 2960-14TT/24TT | 32769 | 0005.5E4E.714B | Root Bridge |
| SW2 | 2960-24TT | 32769 | 00D0.5882.4834 | Non-root |
| SW3 | 2960-24TT | 32769 | 000C.8519.6EBA | Non-root |
| SW4 | 2960-24TT | 32769 | 00E0.A381.AD46 | Non-root |
| Hub0 | Hub-PT | N/A | N/A | Shared medium |
| Hub1 | Hub-PT | N/A | N/A | Shared medium |

---

## Lab Questions

**1. Which switch is the root bridge? Use the CLI to examine the port role/state of each interface on the root. What appears different than what you have learned about the root bridge? What is the cause of this?**

**Root Bridge:** SW1 (MAC 0005.5E4E.714B, Priority 32769)

**What's different:** On a classic STP root bridge, you expect all ports to be Designated/Forwarding. In this topology, SW1 — the root bridge — has a port in a non-designated role:

- **Fa0/3:** Role **Back** (Backup), Status **BLK** (Blocking), Type **Shr** (Shared)

All other ports on SW1 are Designated/Forwarding:
- **Fa0/1:** Desg/FWD, P2p
- **Fa0/2:** Desg/FWD, Shr
- **Fa0/24:** Desg/FWD, Shr

**Cause:** The presence of a **shared medium** (Hub0/Hub1) creates a multi-access segment. RSTP places one port in the Backup role on shared segments to prevent duplicate forwarding. The Backup port blocks because it's receiving its own BPDUs back from the hub, signaling another path to the root on the same segment.

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-22-Lab-Rapid-STP-1.png">
  </a>
</p>

---

**2. Without using the CLI, determine the port role/state of each remaining switch interface. Use the CLI to confirm.**

**Predictions:**

**SW2:**
- Links toward root: expect Root port on best path
- Links toward hosts: expect Designated ports
- Alternate ports block if redundant paths exist

**SW3:**
- Closest to root through Hub1 path
- Expect Root port toward SW1
- Access port to PC3: Designated/Forwarding

**SW4:**
- Two paths toward root
- Best path becomes Root port
- Backup path becomes Alternate/Blocking
- Access port to PC6: Designated/Forwarding

**Confirmed via CLI:**
- SW3: Fa0/2 Root/FWD toward root
- SW4: Fa0/1 Root/FWD, Fa0/2 Altn/BLK (backup path)
- SW2: mixed Designated/Root depending on direction

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-22-Lab-Rapid-STP-2.1.png">
     <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-22-Lab-Rapid-STP-2.2.png">
     <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-22-Lab-Rapid-STP-2.3.png">
     <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-22-Lab-Rapid-STP-2.4.png">
  </a>
</p>

---

**3. Manually configure the appropriate RSTP link type on each interface. What do you think is the correct link type for SW1's F0/24?**

**RSTP Link Types:**
- **P2p (Point-to-Point):** Full-duplex links between switches. Auto-derived by RSTP.
- **Shr (Shared):** Half-duplex or hub-connected segments. RSTP doesn't treat these as edge.
- **Edge:** Ports connected to end devices (PCs, servers). Should be configured with `spanning-tree portfast`.

**Current misconfigurations observed:**
- SW1 Fa0/24 shows as **Shr** but connects to a PC → should be **Edge** or at minimum **P2p** if full-duplex
- Ports connected to hubs correctly show as **Shr**

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
     <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-22-Lab-Rapid-STP-3.1.png">
      <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-22-Lab-Rapid-STP-3.2.png">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-22-Lab-Rapid-STP-3.3.png">
       <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-22-Lab-Rapid-STP-3.4.png">
  </a>
</p>

**Correct link type for SW1's F0/24:**
Since F0/24 connects to a PC (end device), the correct RSTP configuration is:

```cisco
interface FastEthernet0/24
 switchport mode access
 spanning-tree portfast
 spanning-tree link-type point-to-point
```

Or simply enable PortFast, which implicitly treats it as edge:

```cisco
interface FastEthernet0/24
 switchport mode access
 spanning-tree portfast
```

**Why not Shr:** Shared link type is for half-duplex hub segments. A single PC on a modern full-duplex switchport is a point-to-point link toward an edge device.

---

## Commands Practiced

```cisco
! Verification
show spanning-tree active
show spanning-tree interface <type> <number>
show running-config interface <type> <number>

! Link-type override
spanning-tree link-type {point-to-point | shared}

! Edge port configuration
spanning-tree portfast
spanning-tree bpduguard enable

! Access port baseline
switchport mode access
```

---

## What I Learned

The root bridge isn't always perfectly clean. Classic STP theory says the root bridge has all Designated ports. That's true in ideal full-duplex mesh topologies.

But this lab proved: **add a hub, break the rule.** The shared segment creates a Backup port on the root bridge itself because RSTP detects its own BPDUs looping back through the hub. That's not a misconfiguration — it's correct behavior protecting against forwarding duplicates.

The second lesson: **link type matters.** Running `show spanning-tree` and seeing "Shr" on a PC-facing port means that port is being treated as part of a shared collision domain. That's wrong on modern full-duplex links. Fix it with `spanning-tree link-type point-to-point` or `spanning-tree portfast`.

RSTP simplifies STP by using port roles instead of states, but the underlying logic still depends on accurate link-type detection. Misclassified link types slow convergence or create unnecessary alternate ports.

---

## Lab Status

✅ Day 22 Complete

### Topics Covered

* RSTP vs classic STP port roles
* Root bridge behavior with shared segments
* Backup port role on root bridge
* Link-type classification: P2p vs Shared vs Edge
* RSTP auto-derivation of link types
* PortFast as implicit edge signaling
* Hub-created multi-access segments in switched networks

---

**Repository:** [Network-Engineering-Labs-CCNA-2026](https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026)
