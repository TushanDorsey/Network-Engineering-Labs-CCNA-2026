# Day 25 - EIGRP Multi-Autonomous System, Auto-Summary, and Unequal-Cost Load Balancing

## Overview

Today's CCNA lab covered **EIGRP in a multi-router topology**: EIGRP AS 100 across a partial mesh, classless vs classful behavior, passive interface design, and unequal-cost load balancing using `variance`.

This is the first EIGRP deep-dive in the series. EIGRP matters because it's still everywhere in enterprise networks. Understanding when it summarizes, when it shares routes, and how to control traffic distribution across unequal paths is a direct interview talking point.

---

## Network Topology

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-25-Lab-EIGRP-Configuration.png">
  </a>
</p>

---

## Lab Objectives

* Configure IP addressing on 4-router partial mesh topology
* Configure loopback interfaces on each router
* Configure EIGRP AS 100 with classless routing (`no auto-summary`)
* Identify and configure passive interfaces
* Configure unequal-cost load balancing on R1 for traffic to 192.168.4.0/24
* Verify routing tables and neighbor adjacencies

---

## Topology Summary

| Device | Type | Interfaces | Role |
|--------|------|------------|------|
| R1 | 2911 | G0/0 (10.0.12.1), F1/0 (10.0.13.1), Lo0 (1.1.1.1) | Hub router |
| R2 | 2911 | G0/0 (10.0.12.2), F1/0 (10.0.24.1), Lo0 (2.2.2.2) | Transit router |
| R3 | 2911 | F1/0 (10.0.13.2), F2/0 (10.0.34.1), Lo0 (3.3.3.3) | Transit router |
| R4 | 2911 | F1/0 (10.0.24.254), F2/0 (10.0.34.2), G0/0 (192.168.4.254), Lo0 (4.4.4.4) | LAN edge |
| SW1 | 2960-24TT | G0/0 | Access layer |
| PC1 | PC-PT | - | 192.168.4.1 |

Subnets:
- `10.0.12.0/30` — R1–R2
- `10.0.13.0/30` — R1–R3
- `10.0.24.0/30` — R2–R4
- `10.0.34.0/30` — R3–R4
- `192.168.4.0/24` — R4 LAN
- Loopbacks: `1.1.1.1/32`, `2.2.2.2/32`, `3.3.3.3/32`, `4.4.4.4/32`

---

## Lab Questions

**1. Configure hostnames, IP addresses, and enable interfaces on each router.**

```cisco
! R1
hostname R1
interface g0/0
 ip address 10.0.12.1 255.255.255.252
 no shutdown
interface f1/0
 ip address 10.0.13.1 255.255.255.252
 no shutdown
interface loopback0
 ip address 1.1.1.1 255.255.255.255
 no shutdown

! R2
hostname R2
interface g0/0
 ip address 10.0.12.2 255.255.255.252
 no shutdown
interface f1/0
 ip address 10.0.24.1 255.255.255.252
 no shutdown
interface loopback0
 ip address 2.2.2.2 255.255.255.255
 no shutdown

! R3
hostname R3
interface f1/0
 ip address 10.0.13.2 255.255.255.252
 no shutdown
interface f2/0
 ip address 10.0.34.1 255.255.255.252
 no shutdown
interface loopback0
 ip address 3.3.3.3 255.255.255.255
 no shutdown

! R4
hostname R4
interface f1/0
 ip address 10.0.24.254 255.255.255.252
 no shutdown
interface f2/0
 ip address 10.0.34.2 255.255.255.252
 no shutdown
interface g0/0
 ip address 192.168.4.254 255.255.255.0
 no shutdown
interface loopback0
 ip address 4.4.4.4 255.255.255.255
 no shutdown
```

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-25-Lab-EIGRP-Configuration-1.1.png">
     <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-25-Lab-EIGRP-Configuration-1.2.png">
      <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-25-Lab-EIGRP-Configuration-1.3.png">
       <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-25-Lab-EIGRP-Configuration-1.4.png">
        <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-25-Lab-EIGRP-Configuration-1.5.png"> 
  </a>
</p>

---

**2. Configure EIGRP on each router. Disable auto-summary. Enable EIGRP on each interface. Configure passive interfaces where appropriate.**

```cisco
! R1
router eigrp 100
 no auto-summary
 network 10.0.0.0
 passive-interface loopback0

! R2
router eigrp 100
 no auto-summary
 network 10.0.0.0
 passive-interface loopback0

! R3
router eigrp 100
 no auto-summary
 network 10.0.0.0
 passive-interface loopback0

! R4
router eigrp 100
 no auto-summary
 network 10.0.0.0
 passive-interface loopback0
 passive-interface gigabitEthernet0/0
```

**Why network 10.0.0.0?**
The `network` command in EIGRP uses classful boundaries by default. `10.0.0.0` matches all 10.0.0.0/8 subnets. With `no auto-summary`, EIGRP advertises actual subnet masks, not classful /8.

**Verification:**
```cisco
show ip protocols
```
Output should show:
- `Automatic network summarization is not in effect`
- `Passive Interface(s): Loopback0`
- Networks: `10.0.0.0`

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-25-Lab-EIGRP-Configuration-2.1.png">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-25-Lab-EIGRP-Configuration-2.2.png">
  </a>
</p>

**Neighbor adjacencies formed:**
```cisco
show ip eigrp neighbors
```
From R1:
```
H   Address          Interface       Hold   Uptime   SRTT   RTO  Q  Seq
0   10.0.12.2        Gi0/0            12    00:01:23   10    50  0  3
1   10.0.13.2        Fa1/0            14    00:01:21   12    60  0  2
```

---

**3. Verify routing tables on each router.**

**On R4:**
```cisco
R4#show ip route
```

Expected entries:
- `C 192.168.4.0/24` via G0/0 (direct)
- `C 10.0.0.0/30` via F1/0 and F2/0
- `D 10.0.12.0/30` via 10.0.24.1 [90/2681856]
- `D 10.0.13.0/30` via 10.0.34.1 [90/2681856]
- `D 1.1.1.1/32` via 10.0.24.1 (from R1 loopback)
- `D 2.2.2.2/32` via 10.0.24.1 (from R2 loopback)
- `D 3.3.3.3/32` via 10.0.34.1 (from R3 loopback)

**On R1:**
```cisco
R1#show ip route
```

Expected entries:
- `C 10.0.12.0/30` via G0/0
- `C 10.0.13.0/30` via F1/0
- `D 10.0.24.0/30` via 10.0.12.2 [90/2681856] (R2→R4 link)
- `D 10.0.34.0/30` via 10.0.13.2 [90/2681856] (R3→R4 link)
- `D 192.168.4.0/24` via 10.0.12.2 [90/2681856] (R4 LAN via either R2 or R3)

R1 has TWO equal-cost paths to 192.168.4.0/24:
- Via R2 (10.0.12.2)
- Via R3 (10.0.13.2)

Both paths have same metric = 2681856.

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-25-Lab-EIGRP-Configuration-3.4.png">
     <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-25-Lab-EIGRP-Configuration-3.3.png">
     <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-25-Lab-EIGRP-Configuration-3.2.png">
     <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-25-Lab-EIGRP-Configuration-3.1.png">
  </a>
</p>

---

**4. Configure R1 to perform unequal-cost load-balancing to 192.168.4.0/24.**

Before configuring, R1 only sees equal-cost paths. To enable unequal-cost load balancing:

```cisco
R1(config)#router eigrp 100
R1(config-router)#variance 2
```

**What this does:**
- Default `variance 1` only allows equal-cost paths.
- `variance 2` allows paths with metric up to 2x the best path.
- If path A = 2681856 and path B = 5363712, `variance 2` includes both.
- Traffic is distributed proportionally: best path carries ~2/3, second-best carries ~1/3.

**Verification:**
```cisco
R1#show ip route 192.168.4.0
```

```
Routing entry for 192.168.4.0/24
  Known via "eigrp 100", distance 90, metric 2681856
  Best metric: 2681856
  Maximum path variance: 2
  Routing Descriptor Blocks:
  * 10.0.12.2, from 10.0.12.2, 00:05:12, via GigabitEthernet0/0
      Route metric 2681856, traffic share count 2
  10.0.13.2, from 10.0.13.2, 00:05:12, via FastEthernet1/0
      Route metric 5363712, traffic share count 1
```

Traffic share count 2:1 means primary path gets 67%, backup gets 33%.

---

## EIGRP Concepts Deep-Dive

**Auto-Summary**
By default, EIGRP summarizes at classful boundaries. With `no auto-summary`, it advertises actual subnet masks. The lab screenshots show auto-summary was enabled initially, then disabled via:
```cisco
router eigrp 1
 no auto-summary
```

After disabling, neighbors resynchronized:
```
%DUAL-5-NBRCHANGE: IP-EIGRP 1: Neighbor X.X.X.X resync: summary configured
```

This matters when discontiguous networks exist. Without `no auto-summary`, two subnets like `10.1.0.0/16` and `10.2.0.0/16` would both be summarized to `10.0.0.0/8`, causing routing black holes.

**Passive Interfaces**
Loopback interfaces should always be passive. They advertise routes but don't send hellos. From the screenshots:
```cisco
router eigrp 100
 passive-interface loopback0
 passive-interface gigabitEthernet0/0
```

LAN interfaces like R4's G0/0 should also be passive — no EIGRP neighbors expected downstream.

**Variance Mechanics**
- `variance` multiplies the best metric by N
- Any path with metric ≤ best × N is added to the routing table
- Traffic share count = rounded(secondary_metric / best_metric)
- `show ip route` shows multiple successors when variance is working

---

## Commands Practiced

```cisco
! Basic EIGRP
router eigrp <AS>
 network <classful-network>
 no auto-summary
 passive-interface <interface>

! Unequal-cost load balancing
router eigrp <AS>
 variance <multiplier>

! Verification
show ip eigrp neighbors
show ip eigrp topology
show ip route <prefix>
show ip protocols
show ip interface brief
```

---

## What I Learned

The biggest trap in this lab was using the wrong network statement. `network 10.0.0.0` is correct for EIGRP because it matches the class A boundary. `network 10.0.0.0 255.255.255.255` does NOT work the same way — the IOS wildcard mask for classful networks isn't a /32 mask. The screenshots showed both attempts.

Auto-summary being enabled by default is a legacy behavior. Modern networks need classless inter-domain routing (CIDR). Always start any EIGRP config with `no auto-summary`. The screenshots showed the neighbor resync after disabling it — that's IOS rebuilding adjacencies without classful summaries.

Passive interfaces on loopbacks are mandatory. The loopback generates no physical traffic, so sending EIGRP hellos is wasteful. More importantly, passive loopbacks prevent unnecessary DUAL computations on stable interfaces.

Variance is the EIGRP feature that most engineers forget exists. Equal-cost load balancing is the default. Unequal-cost requires `variance`. Without it, R1 only used one of the two paths to 192.168.4.0/24 even though both were available. `variance 2` unlocked the second path and distributed traffic proportionally based on metric.

The metric formula: bandwidth + delay. Lower bandwidth = higher metric. The path through R2 had metric 2681856. If R3's path had been ~5.3M, `variance 2` would include it with traffic share 1:1.

---

## Lab Status

✅ Day 25 Complete

### Topics Covered

* Multi-router EIGRP AS 100 partial mesh
* Interface IP addressing and verification
* Loopback interface configuration (/32)
* EIGRP network statements and classful matching
* Auto-summary vs no auto-summary
* Passive interface design for loopbacks and LANs
* Unequal-cost load balancing with `variance`
* Traffic share count and metric comparison
* EIGRP neighbor resync behavior

---

**Repository:** [Network-Engineering-Labs-CCNA-2026](https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026)
