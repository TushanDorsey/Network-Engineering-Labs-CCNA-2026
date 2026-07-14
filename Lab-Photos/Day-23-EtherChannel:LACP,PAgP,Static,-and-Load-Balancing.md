# Day 23 - EtherChannel: LACP, PAgP, Static, and Load Balancing

## Overview

Today's CCNA lab covered **port channeling** in depth: Layer 2 EtherChannel with LACP and PAgP, Layer 3 routed EtherChannel, troubleshooting bundling failures, and global load-balancing configuration.

This is one of the most practical labs in the entire CCNA track. Real networks use EtherChannel everywhere — between access switches and distribution switches, between core routers, between hypervisor hosts and ToR switches. Understanding protocol negotiation and load balancing is a daily job skill.

---

## Network Topology

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-23-Lab-EtherChannel.png">
  </a>
</p>

---

## Lab Objectives

* Configure Layer 2 EtherChannel between ASW1 and DSW1 using LACP
* Configure Layer 2 EtherChannel between ASW2 and DSW2 using PAgP
* Configure Layer 3 EtherChannel between DSW1 and DSW2 using static mode
* Configure routes to allow PCs to reach SRV1
* Identify default EtherChannel load-balancing method
* Configure global EtherChannel load-balancing based on source/destination IP

---

## Topology Summary

| Device | Type | Interfaces | Role |
|--------|------|------------|------|
| ASW1 | 2960-24TT | G0/1, G0/2, G1/0/3, G1/0/4 | Access switch |
| ASW2 | 2960-24TT | G0/1, G0/2, G1/0/3, G1/0/4 | Access switch |
| DSW1 | 3650-24PS | G1/0/1, G1/0/2, G1/0/3, G1/0/4 | Distribution switch |
| DSW2 | 3650-24PS | G1/0/1, G1/0/2, G1/0/3, G1/0/4 | Distribution switch |
| PC1/PC2 | PCs | - | 172.16.1.0/24 |
| SRV1 | Server | - | 172.16.2.0/24 |

Subnets:
- `172.16.1.0/24` — ASW1 VLAN
- `172.16.2.0/24` — ASW2 VLAN
- `10.0.0.0/30` — DSW1-to-DSW2 routed link

---

## Lab Questions

**1. Configure Layer 2 EtherChannel between ASW1 and DSW1 using LACP. Configure as trunk.**

**Layer 2 + LACP port-channel:**
```cisco
! On ASW1
conf t
interface range GigabitEthernet1/0/3 - 4
 channel-group 1 mode active
 spanning-tree portfast trunk
 no shutdown
interface Port-channel1
 switchport trunk encapsulation dot1q
 switchport mode trunk

! On DSW1
conf t
interface range GigabitEthernet1/0/3 - 4
 channel-group 1 mode active
 no shutdown
interface Port-channel1
 switchport trunk encapsulation dot1q
 switchport mode trunk
```

**Verification:**
```cisco
show etherchannel summary
show interfaces port-channel 1
show interfaces trunk
```

**Flags:**
- `P` = port in use/bundled
- `S` = Layer 2
- `U` = in use
- `Po1(SU)` = Layer 2 port-channel, active

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-23-Lab-EtherChannel-1.1.png">
  <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-23-Lab-EtherChannel-1.2.png">
  </a>
</p>

---

**2. Configure Layer 2 EtherChannel between ASW2 and DSW2 using PAgP. Configure as trunk.**

```cisco
! On ASW2
conf t
interface range GigabitEthernet1/0/3 - 4
 channel-group 1 mode desirable
 spanning-tree portfast trunk
 no shutdown
interface Port-channel1
 switchport trunk encapsulation dot1q
 switchport mode trunk

! On DSW2
conf t
interface range GigabitEthernet1/0/3 - 4
 channel-group 1 mode desirable
 no shutdown
interface Port-channel1
 switchport trunk encapsulation dot1q
 switchport mode trunk
```

**What happens if you try to form a channel with mismatched DTP modes?**
Syncing desirable with active causes drop to `SD` (Suspended) state. The summary shows ports as `(I)` stand-alone instead of `(P)` bundled.

**Error observed:**
```
%EC-5-CANNOT_BUNDLE2: Gig0/1 is not compatible with Gig0/2
and will be suspended (dtp mode of Gig0/1 is on, Gig0/2 is off)
```

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-23-Lab-EtherChannel-2.1.png">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-23-Lab-EtherChannel-2.2.png">
  </a>
</p>

---

**3. Configure Layer 3 EtherChannel between DSW1 and DSW2 using static mode.**

```cisco
! On DSW1
conf t
interface range GigabitEthernet1/0/1 - 2
 channel-group 12 mode on
 no switchport
 ip address 10.0.0.1 255.255.255.252
 no shutdown
interface Port-channel12
 no switchport
 ip address 10.0.0.1 255.255.255.252

! On DSW2
conf t
interface range GigabitEthernet1/0/1 - 2
 channel-group 12 mode on
 no switchport
 ip address 10.0.0.2 255.255.255.252
 no shutdown
interface Port-channel12
 no switchport
 ip address 10.0.0.2 255.255.255.252
```

**Verification:**
```cisco
show ip interface brief
show etherchannel summary
```

Output should show `Port-channel12` with IP address 10.0.0.1/2 and status `up/up`.

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-23-Lab-EtherChannel-3.1.png">
  </a>
</p>

---

**4. Configure routes to allow PCs to reach SRV1.**

```cisco
! On DSW2
conf t
ip route 172.16.1.0 255.255.255.0 10.0.0.1

! SVI route on ASW1 is handled by ASW1's own SVI.
! PCs in 172.16.1.0/24 already have gateway .254 on DSW1 VLAN1.
! DSW1 already knows 172.16.2.0/24 if configured, or needs static:
conf t
ip route 172.16.2.0 255.255.255.0 10.0.0.2
```

**Verification:**
```cisco
show ip route
ping 172.16.2.1
```

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-23-Lab-EtherChannel-4.1.png">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-23-Lab-EtherChannel-4.2.png">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-23-Lab-EtherChannel-4.3.png">
  </a>
</p>

---

**5. What is the default EtherChannel load-balancing method on each switch?**

The default depends on platform:
- **Catalyst 2960/3560/3650:** `src-mac` by default
- Verified with:
```cisco
show etherchannel load-balance
```
Output showed `src-mac` loaded initially on ASW1 and DSW1 before any manual configuration.

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-23-Lab-EtherChannel-5.1.png">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-23-Lab-EtherChannel-5.2.png">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-23-Lab-EtherChannel-5.3.png">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-23-Lab-EtherChannel-5.4.png">
    
  </a>
</p>

---

**6. Configure switches to load-balance based on source and destination IP addresses.**

```cisco
! On ASW1
conf t
port-channel load-balance src-dst-ip

! On ASW2
conf t
port-channel load-balance src-dst-ip

! On DSW1
conf t
port-channel load-balance src-dst-ip

! On DSW2
conf t
port-channel load-balance src-dst-ip
```

**Operational output after change:**
```cisco
show etherchannel load-balance
```

```
EtherChannel Load-Balancing Operational State (src-dst-ip):
Non-IP: Source XOR Destination MAC address
IPv4:   Source XOR Destination IP address
IPv6:   Source XOR Destination IP address
```

Note: configurations are global and affect ALL port-channels on the switch. There is no per-channel load-balancing method on most Catalyst platforms.

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-23-Lab-EtherChannel-6.1.png">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-23-Lab-EtherChannel-6.2.png">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-23-Lab-EtherChannel-6.3.png">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-23-Lab-EtherChannel-6.4.png">
  </a>
</p>

---

## Troubleshooting: "Why Won't My EtherChannel Form?"

| Symptom | Cause | Fix |
|---------|-------|-----|
| Po1(SD) / ports show `(I)` | DTP mismatch between ends | Match channel mode: `active/active`, `desirable/desirable`, `on/on` |
| Po1(bundle unmatched mismatch) | Speed/duplex mismatch | Set both sides to same speed/duplex |
| Po1(pending) | Configuration mismatch on trunk settings | Ensure `switchport trunk encapsulation dot1q` and `switchport mode trunk` consistent |
| Duplicate IP on Port-channel | Trying to configure IP on member interface | Configure IP only on Port-channel, not physical interfaces |
| Load balance not working | Global setting mismatch across switches | Match `port-channel load-balance` on all switches |

---

## Commands Practiced

```cisco
! Layer 2 EtherChannel — LACP
channel-group 1 mode active

! Layer 2 EtherChannel — PAgP
channel-group 1 mode desirable

! Layer 2 EtherChannel — Static
channel-group 1 mode on

! Layer 3 EtherChannel
conf t
interface range Gi1/0/1 - 2
 channel-group 12 mode on
 no switchport
 ip address x.x.x.x 255.255.255.252
!
interface Port-channel12
 no switchport
 ip address x.x.x.x 255.255.255.252

! Trunk inside port-channel
switchport trunk encapsulation dot1q
switchport mode trunk

! Verification
show etherchannel summary
show etherchannel load-balance
show interfaces port-channel 1
show interfaces trunk
show ip route

! Global load-balancing
port-channel load-balance src-dst-ip
```

---

## What I Learned

LACP vs PAgP matters in real environments. If one side is mismatched, the channel doesn't form — it doesn't gracefully fall back to the other protocol. You get standby ports that don't bundle, which silently halves bandwidth and breaks redundancy.

The troubleshooting skill here is reading the summary flags correctly:
- `(P)` = bundled, good
- `(I)` = stand-alone, bad
- `(D)` = down
- `(S)` = Layer 2, `(R)` = Layer 3

Static EtherChannel (`mode on`) is compatible with anything but provides no negotiation. It's useful in labs, but in production, use LACP. It tells you when something breaks.

Load balancing symmetry across switches is critical. If ASW1 uses `src-dst-ip` and DSW1 uses `src-mac`, the hash calculations differ on each side of the same bundle. This causes uneven traffic distribution and can overload one physical link. Always verify global setting matches on both ends.

Layer 3 EtherChannel is just a routed Port-channel. Same channel-group mechanics, but instead of `switchport mode trunk`, you use `no switchport` and assign the IP to the Port-channel interface.

Static route configuration tied to a Layer 3 EtherChannel gives you high availability without dynamic routing. If one physical link drops, the router still has the second link. No reconvergence needed.

---

## Lab Status

✅ Day 23 Complete

### Topics Covered

* Layer 2 EtherChannel with LACP and PAgP
* Layer 3 routed EtherChannel with static mode
* DTP trunk negotiation inside port-channels
* Static route configuration for inter-VLAN reachability
* Default EtherChannel load-balancing behavior
* Global load-balancing to src-dst-ip
* EtherChannel failure modes and flag interpretation
* Debugging channel mismatches

---

**Repository:** [Network-Engineering-Labs-CCNA-2026](https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026)
