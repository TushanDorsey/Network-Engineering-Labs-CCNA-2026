# Day 11 - Configuring Static Routes

## Overview

Today's CCNA lab focused on one of the most important topics in networking—**Static Routing**.

Unlike previous labs where devices only communicated within directly connected networks, this lab required configuring multiple routers to forward traffic between remote networks.

By manually creating static routes, I enabled communication between two LANs connected through three Cisco routers.

This exercise helped reinforce how routers make forwarding decisions and how packets traverse multiple hops to reach their destination.

---

# Network Topology

<p align="center">
<img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2011%20Lab%20-%20Configuring%20Static%20Routes%20.png" width="1200">
</p>

Topology consists of:

- 3 Cisco 2911 Routers
- 2 Cisco 2960 Switches
- 2 End Devices
- 3 Routed Networks
- 2 LAN Networks

---

# Lab Objectives

During this lab I successfully completed:

- Configure router hostnames
- Configure IPv4 addressing
- Configure gateway addresses
- Configure router interfaces
- Configure static routes
- Verify routing tables
- Verify interface status
- Perform end-to-end connectivity testing
- Troubleshoot routing issues

---

# Network Addressing

## LAN 1

```
192.168.1.0/24
```

Router Interface

```
192.168.1.254
```

PC1

```
192.168.1.1
```

---

## Router Link

```
192.168.12.0/24
```

R1

```
192.168.12.1
```

R2

```
192.168.12.2
```

---

## Router Link

```
192.168.13.0/24
```

R2

```
192.168.13.2
```

R3

```
192.168.13.3
```

---

## LAN 2

```
192.168.3.0/24
```

Gateway

```
192.168.3.254
```

PC2

```
192.168.3.1
```

---

# Step 1 — Configure Router Interfaces

<p align="center">
<img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2011%20Lab%20-%20Configuring%20Static%20Routes-1.3.png" width="1200">
  <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2011%20Lab%20-%20Configuring%20Static%20Routes-1.4.png" width="1200">
  <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2011%20Lab%20-%20Configuring%20Static%20Routes-1.5.png" width="1200">
</p>

Each router interface was configured with its assigned IPv4 address.

Commands practiced

```Cisco
interface g0/0

ip address

no shutdown
```

---

# Step 2 — Configure PCs

<p align="center">
<img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2011%20Lab%20-%20Configuring%20Static%20Routes-1.png" width="1200">
<img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2011%20Lab%20-%20Configuring%20Static%20Routes-1.2.png" width="1200">
</p>

Configured

- IP Address
- Subnet Mask
- Default Gateway

Verified using

```
ipconfig
```

---

# Step 3 — Configure Static Routes

The major objective of today's lab.

Each router was configured with routes to remote networks.

Example syntax

```Cisco
ip route <destination-network> <mask> <next-hop>
```

This allows routers to forward packets toward networks they are not directly connected to.

---

# Step 4 — Verify Routing Tables

<p align="center">
<img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2011%20Lab%20-%20Configuring%20Static%20Routes-2.1.png" width="1200">
  <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2011%20Lab%20-%20Configuring%20Static%20Routes-2.2.png" width="1200">
  <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2011%20Lab%20-%20Configuring%20Static%20Routes-2.3.png" width="1200">
</p>

Using

```Cisco
show ip route
```

I confirmed:

- Connected routes
- Local routes
- Static routes

Routing table entries included

```
C

Connected

L

Local

S

Static
```

Understanding the routing table is one of the most important troubleshooting skills for network engineers.

---

# Step 5 — Verify Interface Status

Commands used

```Cisco
show ip interface brief
```

Confirmed:

- Interface Status
- Protocol Status
- Assigned IP Addresses

---

# Step 6 — End-to-End Connectivity

<p align="center">
<img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2011%20Lab%20-%20Configuring%20Static%20Routes-3.png" width="1200">
</p>

Connectivity testing included

PC1 →

Gateway

Gateway →

Remote Router

PC1 →

PC2

PC2 →

Gateway

All pings completed successfully with zero packet loss.

---

# Commands Practiced

```Cisco
ip address

interface

no shutdown

show ip route

show ip interface brief

ping

ip route
```

---

# Skills Practiced

- Static Routing
- Routing Tables
- Packet Forwarding
- Layer 3 Networking
- Cisco IOS
- IPv4 Addressing
- Router Configuration
- Network Verification
- Troubleshooting
- End-to-End Connectivity

---

# What I Learned

Today's lab introduced one of the core responsibilities of a network engineer—teaching routers how to reach remote networks.

Unlike switches that simply forward frames within a LAN, routers require routing information to determine where packets should be sent.

By manually configuring static routes, I learned how routers build routing tables and make forwarding decisions based on destination networks.

I also reinforced the importance of verifying configurations using `show ip route` and validating connectivity with successful end-to-end ping tests.

This lab lays the foundation for learning dynamic routing protocols such as RIP, OSPF, and EIGRP.

---

# Lab Status

✅ Completed Successfully

## Topics Covered

- Static Routing
- Router Configuration
- IPv4 Addressing
- Routing Tables
- Cisco IOS
- End-to-End Connectivity
- Network Troubleshooting

---

## Next Lab

➡ Dynamic Routing (RIP)
