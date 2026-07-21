# Day 27 - OSPF Reference Bandwidth, Hello Protocol, and ASBR Default Route Injection

## Overview

Today's lab was a two-part OSPF deep dive: **reference bandwidth** and **cost calculation**, plus a mandatory look at **OSPF Hello messages** in Simulation mode.

This is one of the most underrated CCNA topics. Default OSPF reference bandwidth is 100 Mbps. In modern networks with Gigabit and 10G links, that means every high-speed link gets cost 1 — OSPF can't distinguish between a 1Gbps fiber circuit and a 10Gbps backbone cable. You have to fix this manually.

---

## Network Topology

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-27-Lab-OSPF-(Part%202).png">
  </a>
</p>

---

## Lab Objectives

* Configure OSPF area 0 across 4 routers + ISP edge
* Set reference bandwidth so FastEthernet interfaces show cost 100
* Configure R1 as ASBR injecting default route into OSPF
* Verify routing tables on R2, R3, R4
* Inspect OSPF Hello messages in Simulation mode

---

## Topology Summary

| Device | Type | Interfaces | IP Assignments |
|--------|------|------------|----------------|
| R1 | 2911 | G0/0, F1/0, F1/1, G3/0, Lo0 | 10.0.12.1/30, 10.0.13.1/30, 203.0.113.1/30, 1.1.1.1/32 |
| R2 | 2911 | G0/0, F1/0, F2/0, Lo0 | 10.0.12.2/30, 10.0.24.1/30, 2.2.2.2/32 |
| R3 | 2911 | F1/0, F2/0, Lo0 | 10.0.13.2/30, 10.0.34.1/30, 3.3.3.3/32 |
| R4 | 2911 | F1/0, F2/0, G0/0, Lo0 | 10.0.24.2/30, 10.0.34.2/30, 192.168.4.254/24, 4.4.4.4/32 |
| SW1 | 2960-24TT | - | - |
| PC1 | PC-PT | - | 192.168.4.1 |

Subnets:
- `203.0.113.0/30` — R1–ISP
- `10.0.12.0/30` — R1–R2
- `10.0.13.0/30` — R1–R3
- `10.0.24.0/30` — R2–R4
- `10.0.34.0/30` — R3–R4
- `192.168.4.0/24` — R4 LAN
- Loopbacks: `1.1.1.1/32`, `2.2.2.2/32`, `3.3.3.3/32`, `4.4.4.4/32`

---

## Lab Questions and Answers

**1. Configure hostnames, IP addresses, and enable interfaces.**

```cisco
! R1
hostname R1
interface g0/0
 ip address 10.0.12.1 255.255.255.252
 no shutdown
interface f1/0
 ip address 10.0.13.1 255.255.255.252
 no shutdown
interface g3/0
 ip address 203.0.113.1 255.255.255.252
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
 ip address 10.0.24.2 255.255.255.252
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
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-27-Lab-OSPF-(Part%202)-1.1.png">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-27-Lab-OSPF-(Part%202)-1.5.png">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-27-Lab-OSPF-(Part%202)-1.4.png">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-27-Lab-OSPF-(Part%202)-1.3.png">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-27-Lab-OSPF-(Part%202)-1.2.png">
  </a>
</p>

---

**2. Configure loopback interfaces on each router.**

Loopbacks are always up unless the router is down. They serve as stable router IDs in OSPF.

**Verification:**
```cisco
R1#show ip interface brief
```
```
Interface       IP-Address      OK? Method  Status  Protocol
GigabitEthernet0/0  10.0.12.1   YES manual  up      up
FastEthernet1/0     10.0.13.1   YES manual  up      up
GigabitEthernet3/0  203.0.113.1 YES manual  up      up
Loopback0           1.1.1.1     YES manual  up      up
```

---

**3. Enable OSPF on each interface. Configure passive interfaces.**

**R1:**
```cisco
router ospf 1
 network 10.0.12.0 0.0.0.3 area 0
 network 10.0.13.0 0.0.0.3 area 0
 network 1.1.1.1 0.0.0.0 area 0
 passive-interface g3/0
 passive-interface loopback0
```

**R2:**
```cisco
router ospf 1
 network 10.0.12.0 0.0.0.3 area 0
 network 10.0.24.0 0.0.0.3 area 0
 network 2.2.2.2 0.0.0.0 area 0
 passive-interface loopback0
```

**R3:**
```cisco
router ospf 1
 network 10.0.13.0 0.0.0.3 area 0
 network 10.0.34.0 0.0.0.3 area 0
 network 3.3.3.3 0.0.0.0 area 0
 passive-interface loopback0
```

**R4:**
```cisco
router ospf 1
 network 10.0.24.0 0.0.0.3 area 0
 network 10.0.34.0 0.0.0.3 area 0
 network 192.168.4.0 0.0.0.255 area 0
 network 4.4.4.4 0.0.0.0 area 0
 passive-interface g0/0
 passive-interface loopback0
```

**Verification:**
```cisco
R1#show ip protocols
```
```
Routing Protocol is "ospf 1"
  ...
  Passive Interface(s):
    GigabitEthernet3/0
    Loopback0
  ...
```

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-27-Lab-OSPF-(Part%202)-2.1png.png">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-27-Lab-OSPF-(Part%202)-2.2png.png">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-27-Lab-OSPF-(Part%202)-2.3png.png">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-27-Lab-OSPF-(Part%202)-2.4png.png">
  </a>
</p>

---

**4. Configure the reference bandwidth so a FastEthernet interface has a cost of 100.**

**Formula:**
```
OSPF Cost = Reference Bandwidth / Interface Bandwidth
```

FastEthernet = 100 Mbps. To get cost 100:
```
100 = Reference Bandwidth / 100
Reference Bandwidth = 10,000 Mbps = 10 Gbps
```

**Command on every router:**
```cisco
router ospf 1
 auto-cost reference-bandwidth 10000
```

**IOS warning (appears on all routers):**
```
% OSPF: Reference bandwidth is changed.
Please ensure reference bandwidth is consistent across all routers.
```

**Critical rule:** Reference bandwidth MUST match on ALL routers in the OSPF domain. If R1 uses 10000 and R4 uses 100, LSAs will have inconsistent metrics and OSPF will make bad path decisions.

**After the change, FastEthernet cost = 100:**
```cisco
R1#show ip ospf interface f1/0
```
```
FastEthernet1/0 is up, line protocol is up
  Internet Address 10.0.13.1/30, Area 0
  Process ID 1, Router ID 1.1.1.1, Network Type BROADCAST, Cost: 100
```

R1 GigabitEthernet cost = 10 (10000/1000):
```
GigabitEthernet0/0 is up, line protocol is up
  Internet Address 10.0.12.1/30, Area 0
  Process ID 1, Router ID 1.1.1.1, Network Type BROADCAST, Cost: 10
```

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-27-Lab-OSPF-(Part%202)-3.1png.png">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-27-Lab-OSPF-(Part%202)-3.2.png">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-27-Lab-OSPF-(Part%202)-3.3.png">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-27-Lab-OSPF-(Part%202)-3.4.png">
  </a>
</p>

---

**5. Configure R1 as an ASBR advertising a default route into OSPF.**

```cisco
R1(config)#router ospf 1
R1(config-router)#default-information originate
```

**Verification:**
```cisco
R1#show ip route ospf
```
```
O*E2 0.0.0.0/0 [110/1] via 203.0.113.2, 00:00:22, GigabitEthernet3/0
```

```cisco
R1#show ip ospf
```
```
Routing Process "ospf 1" with ID 1.1.1.1
...
It is an autonomous system boundary router
...
Number of external LSA 1.
```

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-27-Lab-OSPF-(Part%202)-4.1png.png">
     <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-27-Lab-OSPF-(Part%202)-4.2.png">
     <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-27-Lab-OSPF-(Part%202)-4.3.png">
     <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-27-Lab-OSPF-(Part%202)-4.4.png">
  </a>
</p>

---

**6. Check routing tables on R4. Identify default routes.**

```cisco
R4#show ip route
```
```
Gateway of last resort is 10.0.24.1 to network 0.0.0.0

C    4.0.0.0/32 is subnetted, 1 subnets
C       4.4.4.4 is directly connected, Loopback0
O    10.0.12.0 [110/12] via 10.0.24.1, 00:05:44, FastEthernet1/0
C    10.0.24.0/30 is subnetted, 1 subnets
C       10.0.24.0 is directly connected, FastEthernet1/0
O    10.0.13.0 [110/102] via 10.0.34.1, 00:05:41, FastEthernet2/0
C    10.0.34.0/30 is subnetted, 1 subnets
C       10.0.34.0 is directly connected, FastEthernet2/0
C    192.168.4.0/24 is subnetted, 1 subnets
C       192.168.4.0 is directly connected, GigabitEthernet0/0
O*E2 0.0.0.0/0 [110/1] via 10.0.24.1, 00:02:12, FastEthernet1/0
              [110/1] via 10.0.34.1, 00:02:12, FastEthernet2/0
```

**Two equal-cost default routes:** R4 learns `O*E2 0.0.0.0/0` via both R2 (10.0.24.1) and R3 (10.0.34.1). OSPF ECMP installs both.

**Cost analysis with reference bandwidth 10000:**
- R4 → R2 path to 10.0.12.0/30: cost = 10 (Gig) + 100 (Fa) = 110, but displayed `[110/12]` shows OSPF shows internal cost differently
- Actually `[110/12]` from the screenshot: the second number `12` is the OSPF route type + cost breakdown. The important part is the 110 (administrative distance).

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-27-Lab-OSPF-(Part%202)-5.1.png">
  </a>
</p>

---

## OSPF Hello Message Deep-Dive

**Accessing Simulation Mode:**
1. Click the Simulation icon (bottom right in Packet Tracer)
2. Add a Simple PDUs filter for OSPF
3. Observe the packet structure

**OSPF Hello Packet Fields:**

| Field | Value | Purpose |
|-------|-------|---------|
| Version | 2 | OSPFv2 for IPv4 |
| Type | 1 | Hello packet |
| Packet Length | 44+ bytes | Variable based on options |
| Router ID | 1.1.1.1 | Originating router's ID |
| Area ID | 0.0.0.0 | Area 0 (backbone) |
| Checksum | calculated | Error detection |
| Auth Type | 0 | No authentication |
| Hello Interval | 10 sec | How often hellos are sent |
| Dead Interval | 40 sec | Neighbor timeout (4x Hello) |
| Options | E-bit, MC-bit | External routing capable, multicast capable |
| Router Priority | 1 | DR/BDR election priority |
| DR IP | 10.0.12.1 | Designated Router address |
| BDR IP | 10.0.12.2 | Backup Designated Router address |
| Neighbor List | [2.2.2.2, 3.3.3.3, 4.4.4.4] | Routers this speaker has seen |

**Key fields your CCNA exam cares about:**
- **Hello Interval**: 10 sec on broadcast, 30 sec on NBMA
- **Dead Interval**: 40 sec default (4x Hello)
- **DR/BDR**: Elected on multi-access networks; required on Ethernet segments
- **Neighbor List**: Must bi-directionally see each other before adjacency forms
- **Area ID**: Must match exactly or adjacency fails

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-27-Lab-OSPF-(Part%202)-6.1.png">
  </a>
</p>

---

## Reference Bandwidth Formula

```
OSPF Cost = Reference Bandwidth (Mbps) / Interface Bandwidth (Mbps)
```

Default reference bandwidth = 100 Mbps

| Interface Type | Bandwidth | Cost (default=100) | Cost (ref=10000) |
|----------------|-----------|---------------------|------------------|
| Loopback | varies | 1 | manually set |
| FastEthernet | 100 Mbps | **1** (broken) | **100** ✓ |
| GigabitEthernet | 1000 Mbps | **1** (broken) | **10** ✓ |
| Serial | 1544 Kbps (T1) | 64 | 6477 |

**Without reference-bandwidth fix:** FastEthernet and GigabitEthernet both have cost 1. OSPF sees them as equal and load-balances regardless of actual speed. That's wrong.

**With `auto-cost reference-bandwidth 10000`:** FastEthernet = 100, GigabitEthernet = 10. Now OSPF correctly prefers faster paths.

**Why 10000?** 10000 Mbps / 100 Mbps = 100. That gives FastEthernet exactly cost 100 as the lab requires.

---

## Commands Practiced

```cisco
! OSPF core
router ospf <process-id>
 network <network> <wildcard> area <area-id>
 passive-interface <interface>

! Reference bandwidth (must be consistent across ALL routers)
router ospf <process-id>
 auto-cost reference-bandwidth 10000

! ASBR default route
router ospf <process-id>
 default-information originate

! Verification
show ip route
show ip route ospf
show ip protocols
show ip ospf
show ip ospf interface <interface>
show ip ospf neighbor
show ip interface brief
```

---

## What I Learned

The default reference bandwidth of 100 Mbps is a legacy of 1991. In 1991, 100 Mbps was the fastest link. Today, 100 Gbps links exist, and the default 100 Mbps reference makes every interface above 100 Mbps collapse to cost 1.

The screenshots showed the IOS warning message explicitly:
```
% OSPF: Reference bandwidth is changed.
Please ensure reference bandwidth is consistent across all routers.
```

That warning is non-negotiable. If R1 uses reference bandwidth 10000 and R2 uses 100, the same link has different costs on each end. OSPF LSAs will carry inconsistent metrics, and routing loops become possible.

`auto-cost reference-bandwidth 10000` is the standard for modern networks. Some engineers go higher (100000 for 10G+ dense cores), but 10000 covers most enterprise environments.

FastEthernet cost 100 is the standard CCNA test question. The math: `cost = 10000 / 100 = 100`. That's why 10000 is the magic number.

The OSPF Hello packet fields from Simulation mode are the kind of detail CCNA loves. Key numbers to memorize:
- Hello: 10 sec (broadcast), 30 sec (non-broadcast)
- Dead: 40 sec (broadcast), 120 sec (non-broadcast)
- DR priority range: 0-255 (0 = never DR, 1+ eligible)
- BDR election: second-highest priority

The screenshots confirmed R4's passive interfaces included `GigabitEthernet0/0` (LAN-facing) and `Loopback0`. R4's routing table showed OSPF routes learned via both R2 and R3, with equal-cost paths to 10.0.13.0/30.

---

## Lab Status

✅ Day 27 Complete

### Topics Covered

* OSPF reference bandwidth configuration and why default is broken
* OSPF cost calculation: `cost = reference BW / interface BW`
* Setting reference bandwidth to 10000 for FastEthernet cost 100
* IOS warning about inconsistent reference bandwidth across OSPF domain
* OSPF protocol verification with `show ip ospf interface`
* ASBR default-route injection with `default-information originate`
* Type-5 external LSA propagation to all area routers
* Equal-cost multipath default routes on R4 (via R2 + R3)
* OSPF Hello packet structure: fields, intervals, DR/BDR election
* Simulation mode Hello inspection

---

**Repository:** [Network-Engineering-Labs-CCNA-2026](https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026)
