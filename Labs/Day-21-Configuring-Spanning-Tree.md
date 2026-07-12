# Day 21 - Configuring Spanning Tree

## Overview

Today's CCNA lab focused on **configuring Spanning Tree Protocol (STP)** across a four-switch multi-VLAN topology. Instead of just analyzing port roles, I configured root bridge priorities, adjusted path costs and port priorities, and hardened edge ports with PortFast and BPDU Guard.

This lab is important because real networks don't run on default STP settings. Understanding how to influence root bridge election, control which ports forward, and protect switchports from unauthorized BPDUs is essential for network reliability and security.

---

## Network Topology

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-21-Lab-Configuring-Spanning-Tree.png">
  </a>
</p>


---

## Lab Objectives

* Configure primary/secondary root bridges per VLAN with `spanning-tree vlan X root primary/secondary`
* Verify STP role/state changes across all switches after root bridge reconfiguration
* Influence root port selection by modifying interface cost and port priority
* Secure edge ports with PortFast and BPDU Guard
* Understand per-VLAN STP behavior in a multi-VLAN environment

---

## Topology Summary

| Switch | VLAN1 Bridge ID | VLAN2 Bridge ID | Interfaces |
|--------|-----------------|-----------------|------------|
| SW1 | Priority 24577, MAC 0060.2F90.D14A | Priority 28674, MAC 0060.2F90.D14A | Fa0/1, Fa0/2, Fa0/3 |
| SW2 | Priority 28673, MAC 0001.4301.4B81 | Priority 24578, MAC 0001.4301.4B81 | Fa0/1, Fa0/2, Fa0/3 |
| SW3 | Priority 32769, MAC 0040.0B50.AA56 | Priority 32770, MAC 0040.0B50.AA56 | Fa0/1, Fa0/2, Fa0/3 |
| SW4 | Priority 32769, MAC 0090.0C03.2D70 | Priority 32770, MAC 0090.0C03.2D70 | Fa0/1, Fa0/2, Fa0/3 |

---

## Lab Questions

**1. Use the CLI to check the current STP topology. What is the current root bridge? What is the STP role/state of each port on each switch?**

Initial STP state before configuration changes:

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-21-Lab-Configuring-Spanning-Tree-1.1.png">
  </a>
</p>

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-21-Lab-Configuring-Spanning-Tree-1.2.png">
  </a>
</p>

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-21-Lab-Configuring-Spanning-Tree-1.4.png">
  </a>
</p>

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-21-Lab-Configuring-Spanning-Tree-1.3.png">
  </a>
</p>

**SW1 Initial State:**
- VLAN1: Root bridge (Priority 24577)
- VLAN2: Non-root bridge (Priority 28674)
- Ports: Fa0/1 Designated, Fa0/2 Designated, Fa0/3 Root/Forwarding

**SW2 Initial State:**
- VLAN1: Non-root bridge (Priority 28673)
- VLAN2: Root bridge (Priority 24578)
- Ports: Fa0/1 Designated, Fa0/2 Designated, Fa0/3 Designated

**SW3 Initial State:**
- VLAN1: Non-root bridge (Priority 32769)
- VLAN2: Non-root bridge (Priority 32770)
- Ports vary by direction to root

**SW4 Initial State:**
- VLAN1: Non-root bridge (Priority 32769)
- VLAN2: Non-root bridge (Priority 32770)
- Ports vary by direction to root

---

**2. Configure SW1 as the primary root for VLAN1 and the secondary root for VLAN2. Configure SW2 as the primary root for VLAN2 and the secondary root for VLAN1. What is the STP role/state of each port on each switch now?**

Configuration commands:

```cisco
! On SW1
spanning-tree vlan 1 root primary
spanning-tree vlan 2 root secondary

! On SW2
spanning-tree vlan 2 root primary
spanning-tree vlan 1 root secondary
```

**Result:**
- SW1 becomes root for VLAN1, secondary root for VLAN2
- SW2 becomes root for VLAN2, secondary root for VLAN1
- All switches recalculate port roles based on new root bridge
- Root ports shift to best path toward new root
- Designated ports remain forwarding on LAN segments
- Any former root ports on SW1/SW2 become designated

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-21-Lab-Configuring-Spanning-Tree-2.1.png">
  </a>
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-21-Lab-Configuring-Spanning-Tree-2.2.png">
  </a>
</p>

---

**3. Increase the VLAN1 cost of SW4's F0/2 interface to 100. Does SW4 select a different root port? Why/why not?**

```cisco
interface FastEthernet0/2
 spanning-tree vlan 1 cost 100
```

**Answer:**
This depends on SW4's other available paths to the VLAN1 root. If the alternate path has a higher total path cost than 100, SW4 keeps the current root port. If the alternate path is cheaper, SW4 selects a different root port. STP always chooses the lowest root path cost available.

Key concept: modifying a single interface cost only matters if it changes the total best-path cost comparison.

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-21-Lab-Configuring-Spanning-Tree-4.1.png">
  </a>
</p>

---

**4. Increase the VLAN1 port priority of SW1's F0/1 to 240. Does SW3 select a different root port? Why/why not?**

```cisco
interface FastEthernet0/1
 spanning-tree vlan 1 port-priority 240
```

**Answer:**
Port priority affects port selection when two connected switches have the same path cost to the root. A lower port priority (higher number = lower priority) makes that port less likely to be chosen as root port if the choice is between equivalent-cost paths.

If SW3's path through SW1 was already the best path, changing F0/1 priority to 240 likely won't change the root port unless another path has equal or lower effective priority. Root port selection prefers lower port ID when path costs tie.

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-21-Lab-Configuring-Spanning-Tree-5.1.png">
  </a>
</p>

---

**5. Configure PortFast and BPDU Guard on the F0/3 interfaces of SW3/SW4.**

```cisco
! On SW3
interface FastEthernet0/3
 switchport mode access
 spanning-tree portfast
 spanning-tree bpduguard enable

! On SW4
interface FastEthernet0/3
 switchport mode access
 spanning-tree portfast
 spanning-tree bpduguard enable
```

**Why this matters:**
- **PortFast:** Immediately transitions access ports to forwarding state, skipping listening/learning delays. Essential for end devices that don't speak STP.
- **BPDU Guard:** shuts down the port if a BPDU is received, protecting the switch from rogue switches or bridging loops introduced by user devices.
- Without these, PCs can cause temporary STP reconvergence or allow accidental loops.

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-21-Lab-Configuring-Spanning-Tree-5.2.png">
  </a>
</p>

---

## Commands Practiced

```cisco
! Root bridge configuration
spanning-tree vlan <id> root primary
spanning-tree vlan <id> root secondary

! Interface cost override
spanning-tree vlan <id> cost <value>

! Port priority override
spanning-tree vlan <id> port-priority <value>

! Edge port protection
spanning-tree portfast
spanning-tree bpduguard enable

! Verification
show spanning-tree active
show spanning-tree interface <type> <number>
show running-config interface <type> <number>
```

---

## What I Learned

Configuring STP is different from analyzing it. Analysis tells you what's happening; configuration tells the switch what *should* happen.

The biggest insight from this lab: `root primary` and `root secondary` are convenience macros that set bridge priority to 24576 or 28672 automatically. You don't have to manually calculate priority values.

The second insight: changing interface cost only matters relative to other available paths. If you crank one interface to 100 but the backup path is also expensive, nothing changes. STP compares *best* paths, not individual links.

PortFast and BPDU Guard are non-negotiable on access ports. Without them, a user plugging in a rogue switch creates a broadcast storm within seconds. BPDU Guard is your safety net.

---

## Lab Status

✅ Day 21 Complete

### Topics Covered

* STP root bridge configuration per VLAN
* Primary/secondary root bridge election
* Interface cost modification
* Port priority modification
* PortFast and BPDU Guard configuration
* Multi-VLAN STP behavior
* Verifying STP role state changes across 4-switch topology

---

**Repository:** [Network-Engineering-Labs-CCNA-2026](https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026)
