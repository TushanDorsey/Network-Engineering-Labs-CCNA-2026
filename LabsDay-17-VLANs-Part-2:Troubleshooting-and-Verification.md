# Day 17 - VLANs Part 2: Trunk Allowed Lists, Native VLAN Mismatch, and Router-on-a-Stick

> Evidence-based lab notes written from Packet Tracer screenshots. Each step is image-anchored.

---



## Network Topology

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2017%20Lab%20-%20VLANs%20(Part%202).png" alt="Day 17 Network Topology - VLANs 10, 20, 30">
  </a>
</p>

**Devices:** SW1, SW2, R1, PC2, PC3, PC4, PC5

**VLANs and subnets:**

- **VLAN 10** — Engineering — `10.0.0.0/26` — PC2, PC3
- **VLAN 20** — Sales — `10.0.0.64/26` — PC5
- **VLAN 30** — HR — `10.0.0.128/26` — PC4

---



## Access Ports on SW 1 & 2 

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2017%20Lab%20-%20VLANs%20(Part%202)-1.1.png" alt="SW2 access port assignment and VLAN validation">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2017%20Lab%20-%20VLANs%20(Part%202)-1.2.png" alt="SW2 access port assignment and VLAN validation">
  </a>
</p>

**Switch:** SW2

**Verified assignments:**

- `interface f0/1`
  - `switchport access vlan 20`

- `interface f0/2 - f0/3`
  - `switchport access vlan 10`

**Verification evidence:**

- `show vlan brief` showed VLAN 20 with port `Fa0/1`
- `show vlan brief` showed VLAN 10 with ports `Fa0/2, Fa0/3`
- `show ip interface brief` confirmed those ports `up/up`

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2017%20Lab%20-%20VLANs%20(Part%202)-1.1.png" alt="SW2 topology context: VLAN 10 and VLAN 20 connected devices">
  </a>
</p>

---



## Step 1 - Verify Baseline Trunk Operation

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2017%20Lab%20-%20VLANs%20(Part%202)-2.1.png" alt="Trunk verification on SW1 showing 802.1q and active VLANs">
  </a>
</p>

**Device:** SW1

**Command:**

- `show interfaces trunk`

**Observed:**

- Port `Gig0/1`
- Mode: `on`
- Encapsulation: `802.1q`
- Status: `trunking`
- Native VLAN: `1001`
- VLANs allowed and active in management domain: `1, 10, 30`

---



## Step 2 - Configure Trunk and Check Allowed VLAN Behavior

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2017%20Lab%20-%20VLANs%20(Part%202)-2.1.png" alt="Native VLAN mismatch warning and trunk state outcome">
  </a>
</p>

**Device:** SW1

**Relevant outcome:**

- CDP warned: `%CDP-4-NATIVE_VLAN_MISMATCH: Native VLAN mismatch discovered on GigabitEthernet0/1 (1001), with SW2 GigabitEthernet0/1 (1)`
- Even with that mismatch, the trunk stayed up
- Trunk allowed VLANs remained an important knob even after baseline verification

---



## Step 3 - Configure Trunk Allowed VLANs on SW2

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2017%20Lab%20-%20VLANs%20(Part%202)-2.2.png" alt="SW2 trunk interface configuration history">
  </a>
</p>

**Device:** SW2

**Config verified:**

- `interface g0/1`
  - `switchport mode trunk`
  - `switchport trunk allowed vlan add 10`
  - `switchport trunk allowed vlan add 20`
  - `no shutdown`

- `interface g0/2`
  - `switchport mode trunk`
  - `switchport trunk allowed vlan add 10`
  - `switchport trunk allowed vlan add 20`
  - `switchport trunk allowed vlan add 30`

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2017%20Lab%20-%20VLANs%20(Part%202)-3.1.png" alt="SW2 show interface trunk showing allowed VLANs 10,20,30">
     <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2017%20Lab%20-%20VLANs%20(Part%202)-3.2.png" alt="SW2 show interface trunk showing allowed VLANs 10,20,30">
  </a>
</p>

**Verification:**

- `show interface trunk` showed Trunk allowed VLANs: `10,20,30`

---



## Step 4 - Router-on-a-Stick Verification

<p align="center">
  <a href="PASTE-IMAGE-LINK-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2017%20Lab%20-%20VLANs%20(Part%202)-4.png" alt="Router R1 subinterface verification and ping test">
  </a>
</p>

**Device:** R1

**Verified interfaces:**

- `GigabitEthernet0/0.10`
  - IP assigned and `up/up`

- `GigabitEthernet0/0.20`
  - IP assigned and `up/up`

- `GigabitEthernet0/0.30`
  - IP assigned and `up/up`

**Connectivity check:**

- Ping to `10.0.0.63` succeeded

---



## Connectivity Verification Summary

From the screenshots:

- SW2 access ports match the topology: VLAN 10 on `Fa0/2/3`, VLAN 20 on `Fa0/1`
- SW1 trunk `Gig0/1` is trunking with `802.1q`
- SW2 trunks are allowed VLANs `10,20,30`
- CDP reported native VLAN mismatch between SW1 `1001` and SW2 `1`
- Router subinterfaces for VLANs 10, 20, 30 are up
- Host-to-host and host-to-gateway pings succeeded

---



## Commands Practiced

```cisco
show interfaces trunk
show interfaces <id> switchport
show vlan brief
show ip interface brief
show vlan id <vlan-list>
show cdp neighbors detail
ping <host>

interface <id>
 switchport mode trunk
 switchport trunk allowed vlan add <list>
 switchport trunk native vlan <id>
 no shutdown
```

---



## Skills Practiced

- Trunk allowed-list configuration
- Native VLAN mismatch recognition via CDP
- Access port validation with `show vlan brief`
- Router-on-a-stick subinterface verification
- End-to-end inter-VLAN ping validation

---



## What I Learned

From the actual evidence in these screenshots, the important lesson is not just what to configure, but what to verify after config:

- `show vlan brief` is the fastest proof that access ports landed in the right VLANs
- `show interfaces trunk` tells you both mode and allowed VLANs on the same line
- CDP native VLAN mismatch messages are useful, but the trunk may still stay up
- Router-on-a-stick is not done until the subinterfaces are `up/up` and a VLAN-to-VLAN ping succeeds

---



## Lab Status

**Day 17 Complete**

### Topics Covered

* VLAN access port verification
* Trunk allowed VLANs
* Native VLAN mismatch
* CDP mismatch warning
* Router-on-a-stick subinterfaces
* End-to-end connectivity testing

---



**Repository:** [Network-Engineering-Labs-CCNA-2026](https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026)
