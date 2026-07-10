# Day 20 - Analyzing STP: Port Roles Across Four Switches

## Overview

Today's CCNA lab focused on **Spanning Tree Protocol (STP) analysis** across a four-switch topology. Instead of configuring STP, I analyzed existing port roles to understand how STP elects a root bridge and assigns forwarding/blocking states.

The objective was to identify the root bridge, determine each port's role (Root/Designated/Non-Designated), and verify the theory against live CLI output.

This lab is important because STP is the backbone of loop-free Layer 2 networks. Understanding port roles is essential for troubleshooting broadcast storms, identifying blocked paths, and planning redundant topologies.

---

## Network Topology

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2020%20Lab%20-%20Analyzing%20STP.png">
  </a>
</p>

---

## Lab Objectives

* Identify the root bridge in a multi-switch topology
* Determine STP port roles: Root, Designated, Alternate/Non-Designated
* Verify port roles with `show spanning-tree detail`
* Correlate bridge priority/MAC addresses to root bridge election
* Map logical STP roles to physical topology connections

---

## Lab Questions

> *TURN OFF LINK LIGHTS IN PACKET TRACER FOR THIS LAB*
> Options > Preferences > Show Link Lights

**Which switch is the root bridge?**
- **SW3** (Priority 24577 — lowest bridge ID)

**Identify the role (root/designated/non-designated) of each switch port:**

| Switch | Port | Assigned Role |
|--------|------|---------------|
| SW1 | F0/1 | Non-Designated |
| SW1 | F0/2 | Non-Designated |
| SW1 | F0/3 | Non-Designated |
| SW1 | F0/4 | Root |
| SW2 | F0/1 | Designated |
| SW2 | F0/2 | Designated |
| SW2 | F0/3 | Non-Designated |
| SW2 | G0/1 | Root |
| SW3 | F0/1 | Designated |
| SW3 | F0/2 | Designated |
| SW3 | F0/3 | Designated |
| SW3 | G0/1 | Designated |
| SW4 | G0/1 | Designated |
| SW4 | G0/2 | Root |

*Answers confirmed in CLI with `show spanning-tree detail`.*

---

## STP Topology Summary

| Switch | Priority | MAC Address | Role |
|--------|----------|-------------|-------|
| SW1 | 32769 | 0001.4338.79D8 | Non-root bridge |
| SW2 | 28673 | 0002.16D6.D0B8 | Non-root bridge |
| SW3 | 24577 | 00E0.F9E6.44A5 | **Root Bridge** |
| SW4 | 32769 | 0090.0C01.9587 | Non-root bridge |

**Root Bridge Election Logic:**
- STP elects the root bridge based on lowest Bridge ID
- Bridge ID = Priority + MAC address
- SW3 has the lowest priority (24577), making it the root
- All ports on the root bridge become **Designated Ports**

---

## Step 1 - Identify Root Bridge and Port Roles

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2020%20Lab%20-%20Analyzing%20STP-1.png">
    
  </a>
</p>

Analyze the topology and determine the root bridge by comparing bridge priorities.

**Key Finding:**
- SW3 has the lowest priority: **24577**
- SW3 is elected as the **Root Bridge**
- All ports on SW3 are **Designated Ports** (forwarding)

**Port Role Assignments:**

**SW1 Ports:**
- F0/4: **Root Port** (forwarding) — best path to root
- F0/1: **Alternate** (blocking)
- F0/2: **Alternate** (blocking)
- F0/3: **Alternate** (blocking)

**SW2 Ports:**
- G0/1: **Root Port** (forwarding) — best path to root
- F0/1: **Designated** (forwarding)
- F0/2: **Designated** (forwarding)
- F0/3: **Alternate** (blocking)

**SW3 Ports (Root Bridge):**
- F0/1: **Designated** (forwarding)
- F0/2: **Designated** (forwarding)
- F0/3: **Designated** (forwarding)
- G0/1: **Designated** (forwarding)

**SW4 Ports:**
- G0/2: **Root Port** (forwarding) — best path to root
- G0/1: **Designated** (forwarding)

---

## Step 2 - Verify STP on SW3 (Root Bridge)

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2020%20Lab%20-%20Analyzing%20STP-2.3.png">
  </a>
</p>

Run `show spanning-tree detail` to verify all ports are designated forwarding.

```cisco
show spanning-tree detail
```

Expected output indicators for root bridge:
- Bridge ID: Priority 24577, address 00E0.F9E6.44A5
- All ports show: `designated forwarding`
- Designated root = own bridge ID
- Port path cost varies by interface speed:
  - FastEthernet: path cost 19
  - GigabitEthernet: path cost 4

---

## Step 3 - Verify STP on SW1 (Non-Root Bridge)

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2020%20Lab%20-%20Analyzing%20STP-2.1.png">
  </a>
</p>

Run `show spanning-tree detail` to verify root port and alternate ports.

```cisco
show spanning-tree detail
```

Expected output indicators:
- Root port: `root forwarding` status
- Alternate ports: `alternate blocking` status
- Designated root priority: 24577 (SW3's priority)
- Designated bridge priority: varies (32769 for SW1 ports)

---

## Step 4 - Verify STP on SW2 (Non-Root Bridge)

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2020%20Lab%20-%20Analyzing%20STP-2.2.png">
  </a>
</p>

Run `show spanning-tree detail` to verify mixed designated/root/alternate roles.

```cisco
show spanning-tree detail
```

Expected output indicators:
- Root port: G0/1 (`root forwarding`)
- Designated ports: F0/1, F0/2 (`designated forwarding`)
- Alternate port: F0/3 (`alternate blocking`)
- Designated root priority: 24577

---

## Step 5 - Verify STP on SW4 (Non-Root Bridge)

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2020%20Lab%20-%20Analyzing%20STP-2.4.png">
  </a>
</p>

Run `show spanning-tree detail` to verify root port and designated port.

```cisco
show spanning-tree detail
```

Expected output indicators:
- Root port: G0/2 (`root forwarding`)
- Designated port: G0/1 (`designated forwarding`)
- Designated root priority: 24577

---

## Port Role Verification Summary

| Switch | Port | Role | State | Path Cost |
|--------|------|------|-------|-----------|
| SW3 | F0/1 | Designated | Forwarding | 19 |
| SW3 | F0/2 | Designated | Forwarding | 19 |
| SW3 | F0/3 | Designated | Forwarding | 19 |
| SW3 | G0/1 | Designated | Forwarding | 4 |
| SW1 | F0/4 | Root | Forwarding | 19 |
| SW1 | F0/1 | Alternate | Blocking | 19 |
| SW1 | F0/2 | Alternate | Blocking | 19 |
| SW1 | F0/3 | Alternate | Blocking | 19 |
| SW2 | G0/1 | Root | Forwarding | 4 |
| SW2 | F0/1 | Designated | Forwarding | 19 |
| SW2 | F0/2 | Designated | Forwarding | 19 |
| SW2 | F0/3 | Alternate | Blocking | 19 |
| SW4 | G0/2 | Root | Forwarding | 4 |
| SW4 | G0/1 | Designated | Forwarding | 4 |

---

## Commands Practiced

```cisco
! STP analysis
show spanning-tree
show spanning-tree detail
show spanning-tree interface <type> <number>

! Key output fields
Bridge ID
Root ID
Port path cost
Port priority
Designated root priority
Designated bridge priority
Port role: root/designated/alternate/non-designated
Port state: forwarding/blocking
```

---

## Skills Practiced

* Root bridge identification from bridge priority
* Port role classification: Root, Designated, Alternate
* Interpreting `show spanning-tree detail` output
* Mapping logical STP roles to physical topology
* Understanding path cost differences between FastEthernet and GigabitEthernet
* Recognizing alternate blocking ports as loop prevention

---

## What I Learned

STP analysis is pattern recognition. Once you know the root bridge, every other switch has exactly one root port, and every LAN segment has exactly one designated port. Everything else is either alternate or non-designated blocking.

The key insight from this lab: priority wins the root election, but path cost determines which port becomes the root port on non-root switches. On SW2, both F0/1 and F0/2 are designated (they lead to downstream devices), while F0/3 is alternate because it offers a redundant path to the root that's not needed.

GigabitEthernet interfaces have lower path cost (4) than FastEthernet (19), so the Gig links were preferred as root ports where available. This explains why SW2 and SW4 both chose their Gig interfaces as root ports instead of FastEthernet alternate paths.

---

## Lab Status

✅ Day 20 Complete

### Topics Covered

* STP root bridge election
* Port role analysis: Root, Designated, Alternate
* `show spanning-tree detail` verification
* Path cost comparison across interface speeds
* Four-switch topology loop prevention
* Mapping CLI output to physical topology

---

**Repository:** [Network-Engineering-Labs-CCNA-2026](https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026)
