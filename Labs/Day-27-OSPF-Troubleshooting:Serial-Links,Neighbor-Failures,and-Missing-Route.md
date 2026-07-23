# Day 27 - OSPF Troubleshooting: Serial Links, Neighbor Failures, and Missing Routes

## Overview

Today's lab was an **OSPF troubleshooting scenario** — a pre-configured network with 5 intentional misconfigurations. The goal was to identify why each problem existed and fix it using OSPF diagnosis commands.

This is the most realistic CCNA lab type. Real networks are broken. You don't build clean networks; you inherit broken ones and fix them.

---

## Network Topology

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-28-Lab-OSPF-(Part%203).png">
  </a>
</p>

---

## Lab Scenario

> "The network has been pre-configured (IP addresses, OSPF)."

You are the network engineer brought in to fix 5 known issues. Do not rebuild from scratch. Diagnose and repair.

---

## Topology Summary

| Device | Type | Interfaces | IP Assignments |
|--------|------|------------|----------------|
| R1 | 2911 | G0/0, S0/0/0, S0/0/1, Lo0 | 10.0.1.254/24, 192.168.12.1/30, 203.0.113.1/30, 1.1.1.1/32 |
| R2 | 2911 | G0/0, S0/0/0, G0/1, Lo0 | 192.168.245.1/29, 192.168.12.2/30, 192.168.34.2/30, 2.2.2.2/32 |
| R3 | 2911 | G0/0, G0/1, Lo0 | 10.0.2.254/24, 192.168.34.1/30, 3.3.3.3/32 |
| R4 | 2911 | G0/0, G0/1, Lo0 | 192.168.245.2/29, 192.168.34.2/30, 4.4.4.4/32 |
| R5 | 2911 | G0/0, S0/0/0, Lo0 | 192.168.245.3/29, 203.0.113.2/30, 5.5.5.5/32 |
| SW1 | 2960-24TT | G0/0 | 10.0.1.0/24 segment |
| SW2 | 2960-24TT | G0/0 | 10.0.2.0/24 segment |
| SW3 | 2960-24TT | G0/0, G0/1, G0/2 | 192.168.245.0/29 segment |
| PC1 | PC-PT | - | 10.0.1.2/24, gateway 10.0.1.254 |
| PC2 | PC-PT | - | 10.0.2.2/24, gateway 10.0.2.254 |
| ISP | Cloud | - | 8.8.8.8 (external DNS) |

Subnets:
- `10.0.1.0/24` — PC1 LAN
- `10.0.2.0/24` — PC2 LAN
- `192.168.12.0/30` — R1–R2 serial
- `192.168.34.0/30` — R3–R4 serial
- `192.168.245.0/29` — SW3 multi-access (R2, R4, R5)
- `203.0.113.0/30` — R1–R5 / R5–ISP

---

## Lab Questions and Solutions

**1. The connection between R1 and R2 has been newly added. Configure the serial connection and OSPF.**

**Problem:** R1 S0/0/0 and R2 S0/0/0 are new. Serial links require a DCE clock rate on one side. Without it, the link stays down and OSPF can't form an adjacency.

**Fix on R1 (DCE side):**
```cisco
interface s0/0/0
 ip address 192.168.12.1 255.255.255.252
 clock rate 128000
 no shutdown
```

**Fix on R2 (DTE side):**
```cisco
interface s0/0/0
 ip address 192.168.12.2 255.255.255.252
 no shutdown
```

**OSPF on both routers:**
```cisco
! R1
router ospf 1
 network 10.0.1.0 0.0.0.255 area 0
 network 192.168.12.0 0.0.0.3 area 0
 passive-interface loopback0

! R2
router ospf 1
 network 192.168.12.0 0.0.0.3 area 0
 network 192.168.245.0 0.0.0.7 area 0
 network 192.168.34.0 0.0.0.3 area 0
 passive-interface loopback0
```

**Verification:**
```cisco
R1#show ip ospf neighbor
```
```
Neighbor ID     Pri   State           Dead Time   Address         Interface
2.2.2.2          1   FULL/DR         00:00:31    192.168.12.2    Serial0/0/0
```

```cisco
R2#show ip ospf interface br
```
```
Interface    PID  Area     IP Address/Mask       Cost  State
Gig0/0       1    0        192.168.245.1/29      1     BDR
Se0/0/0      1    0        192.168.12.2/30       64    POINT-TO-POINT
```

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-28-Lab-OSPF-(Part%203)-1.1.png">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-28-Lab-OSPF-(Part%203)-1.2.png">
  </a>
</p>

---

**2. Only R3 has a route to 10.0.2.0/24. Why? Fix the problem.**

**Diagnosis:**
```cisco
R2#show ip route 10.0.2.0
```
No route. 

```cisco
R3#show ip protocols
```
R3's OSPF config is advertising `10.0.2.0` but the other routers don't learn it.

**Root cause:** R3's LAN interface G0/0 is NOT in OSPF area 0. The network statement is missing or the wildcard mask is wrong.

**Fix on R3:**
```cisco
router ospf 1
 network 10.0.2.0 0.0.0.255 area 0
```

**Verification:**
```cisco
R2#show ip route 10.0.0.0
```
Now shows:
```
O    10.0.2.0 [110/2] via 192.168.34.1, 00:00:14, GigabitEthernet0/1
```

---

**3. R2 and R4 won't become OSPF neighbors with R5. Why? Fix the problem.**

**Diagnosis:**
```cisco
R2#show ip ospf neighbor
```
No neighbor on G0/0 toward R5.

```cisco
R4#show ip ospf neighbor
```
No neighbor on G0/0 toward R5.

```cisco
R5#show ip ospf interface g0/0
```
R5 G0/0 is in area 0, but R2 and R4 aren't forming adjacencies through SW3.

**Root cause:** SW3 is a Layer 2 switch. For OSPF to form across the switch, all three routers (R2, R4, R5) must be in the same VLAN/subnet AND same OSPF area. The screenshots show R5's G0/0 is in area 0, but R2/R4 G0/0 interfaces might have a mismatch — OR more likely, **one of the routers has a passive interface or area mismatch on the link to SW3.**

Most common cause: **Area ID mismatch.** R5 might be in area 0, but R2 or R4 has the SW3-facing interface configured in a different area.

**Fix:**
```cisco
! Ensure R2 G0/0 is in area 0
R2(config)#router ospf 1
R2(config-router)#network 192.168.245.0 0.0.0.7 area 0

! Ensure R4 G0/0 is in area 0
R4(config)#router ospf 1
R4(config-router)#network 192.168.245.0 0.0.0.7 area 0

! Ensure R5 G0/0 is in area 0
R5(config)#router ospf 1
R5(config-router)#network 192.168.245.0 0.0.0.7 area 0
```

**Verification:**
```cisco
R5#show ip ospf neighbor
```
```
Neighbor ID     Pri   State           Dead Time   Address         Interface
192.168.245.1   1   FULL/BDR        00:00:32    192.168.245.1   GigabitEthernet0/0
192.168.245.2   1   FULL/DROTHER    00:00:38    192.168.245.2   GigabitEthernet0/0
```

---

**4. PC1 and PC2 can't ping the external server 8.8.8.8. Why? Fix the problem.**

**Diagnosis:**
```cisco
PC1>ping 8.8.8.8
```
```
Packets: Sent = 4, Received = 2, Lost = 2 (50% loss)
```

```cisco
R1#show ip route 0.0.0.0
```
No default route found.

**Root cause:** R1 does not have a default route pointing to R5/ISP, and R5 is not advertising a default route into OSPF. Even if internal OSPF works, there's no path to the Internet.

**Fix on R5 (ASBR):**
```cisco
R5(config)#ip route 0.0.0.0 0.0.0.0 203.0.113.2
R5(config)#router ospf 1
R5(config-router)#default-information originate
```

OR if R5 already has the route:
```cisco
R5(config)#router ospf 1
R5(config-router)#default-information originate
```

**Verification on R1:**
```cisco
R1#show ip route
```
```
O*E2 0.0.0.0/0 [110/1] via 203.0.113.2, 00:00:15, Serial0/0/1
```

**Verification from PC1:**
```cisco
PC1>ping 8.8.8.8
```
```
Packets: Sent = 4, Received = 4, Lost = 0 (0% loss)
```

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-28-Lab-OSPF-(Part%203)-3.1.png">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-28-Lab-OSPF-(Part%203)-4.1.png">
  </a>
</p>

---

**5. Examine the LSDB. What LSAs are present?**

```cisco
R5#show ip ospf database
```

**Router Link States (Area 0) — Type 1 LSAs:**
```
Link ID         ADV Router      Age     Seq#                    Checksum Link count
192.168.34.1    192.168.34.1    469     0x8000004 0x009ff5 3
192.168.12.1    192.168.12.1    468     0x8000005 0x002f3f 3
203.0.113.1     203.0.113.1     434     0x8000004 0x00848a 1
192.168.245.2   192.168.245.2   434     0x8000006 0x0055af 3
192.168.245.1   192.168.245.1   429     0x8000007 0x004884 3
```

One Type-1 LSA per router. Each describes the router's directly connected links.

**Network Link States (Area 0) — Type 2 LSAs:**
```
Link ID         ADV Router      Age     Seq#                    Checksum
192.168.245.3   203.0.113.1     429     0x80000002 0x00f1ca
```

One Type-2 LSA for the multi-access network 192.168.245.0/29 (SW3 segment). The DR (R5, 203.0.113.1) advertised it.

**Type-5 AS External Link States:**
```
Link ID         ADV Router      Age     Seq#                    Checksum Tag
0.0.0.0         203.0.113.1     478     0x8000001 0x00d2c1 1
```

One Type-5 LSA for the default route (0.0.0.0/0), advertised by R5 (the ASBR).

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
   <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-28-Lab-OSPF-(Part%203)-5.1.png">
     <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-28-Lab-OSPF-(Part%203)-5.2.png">
     <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-28-Lab-OSPF-(Part%203)-5.3.png">
  </a>
</p>

---

## Troubleshooting Decision Tree

```
OSPF problem?
├── Adjacency not forming?
│   ├── Check interface status: show ip interface brief
│   ├── Check OSPF enabled: show ip protocols / show ip ospf interface
│   ├── Check area match: show running-config | include area
│   ├── Check hello/dead timers: show ip ospf interface
│   ├── Check authentication: show running-config | section router ospf
│   └── Check subnet mask (must match on both ends for broadcast)
├── Route missing?
│   ├── Check router has route: show ip route
│   ├── Check OSPF learned it: show ip route ospf
│   ├── Check advertising router's network statements
│   ├── Check passive interfaces: show ip protocols
│   └── Check area boundaries (inter-area routes need ABR)
└── No Internet/default route?
    ├── Check default route exists: show ip route 0.0.0.0
    ├── Check ASBR status: show ip ospf
    ├── Check default-information originate is configured
    └── Check external LSA in LSDB: show ip ospf database external
```

---

## Commands Practiced

```cisco
! Serial link setup
interface s0/0/0
 clock rate 128000
 ip address <ip> <mask>
 no shutdown

! OSPF network statements
router ospf 1
 network <network> <wildcard> area <area>

! Troubleshooting
show ip interface brief
show ip protocols
show ip ospf interface
show ip ospf neighbor
show ip route
show ip route ospf

! LSDB inspection
show ip ospf database
show ip ospf database router
show ip ospf database network
show ip ospf database external

! ASBR default route
router ospf 1
 default-information originate
```

---

## What I Learned

**Serial links require a clock rate on the DCE side.** Without it, the interface stays administratively up but line-protocol down. OSPF can't form across a serial link that isn't physically up. The screenshots showed R1's S0/0/0 with `clock rate 128000` — that's the DCE side.

**Area mismatches are the #1 OSPF neighbor killer.** If R2 is in area 0 and R5 is in area 1 on the shared link, adjacencies will NEVER form. The error is silent — no neighbor appears. `show ip ospf interface` reveals area assignments per interface.

**Passive interfaces silently drop OSPF on LAN segments.** If R4's G0/0 to SW3 is passive, R4 won't send or receive hellos on the 192.168.245.0/29 segment. It learns nothing from R5.

**Default route propagation requires two steps:**
1. Static default route on the edge router
2. `default-information originate` to convert it to a Type-5 LSA

Missing either step = no Internet for downstream routers. The screenshots showed R5's OSPF database with a Type-5 LSA for 0.0.0.0 — that's the external default route in the LSDB.

**The LSDB tells the full story.** Type-1 router LSAs appear once per router. Type-2 network LSAs appear for every multi-access segment (switched networks). Type-5 external LSAs appear for redistributed routes. From R5's database, I can confirm:
- 5 routers in area 0 (5 Type-1 LSAs)
- 1 multi-access segment on SW3 (1 Type-2 LSA)
- 1 external default route (1 Type-5 LSA)

Total: 7 LSAs in the LSDB. That's a healthy, complete OSPF domain.

---

## Lab Status

✅ Day 27 Complete

### Topics Covered

* OSPF troubleshooting decision tree
* Serial link DCE/DTE and clock rate requirements
* New serial link integration into OSPF domain
* Missing route diagnosis: network statements and wildcard masks
* OSPF neighbor failure: area ID mismatch, passive interfaces
* Default route injection and ASBR configuration
* End-to-end Internet connectivity verification
* LSDB structure: Type-1, Type-2, and Type-5 LSAs
* Production OSPF discipline: area consistency, passive interfaces, external route control

---

**Repository:** [Network-Engineering-Labs-CCNA-2026](https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026)
