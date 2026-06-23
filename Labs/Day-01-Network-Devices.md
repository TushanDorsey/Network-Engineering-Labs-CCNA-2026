
# Day 01 - Network Devices & Enterprise Topology

## Overview

This lab was completed as part of my CCNA studies using Cisco Packet Tracer.

The purpose of this exercise was to become familiar with common network devices found in enterprise environments and understand how those devices work together to provide connectivity, communication, and security across multiple locations.

This topology simulates a company operating from two separate branch offices:

- New York Branch
- Tokyo Branch

Both locations are connected through the internet while implementing different network security designs.

---

## Network Topology

<p align="center">
  <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-01-Network-Devices.png" alt="Day 01 Network Devices Lab" width="1000">
</p>

<p align="center">
  Enterprise topology consisting of a New York branch, Tokyo branch, perimeter firewalls, routers, switches, servers, workstations, and an external attacker simulation.
</p>

---

## New York Branch

The New York Branch represents a small office environment consisting of user workstations connected to the local network.

### Devices

- PC0
- PC1
- Cisco 2960 Switch (SW1)
- Cisco 2911 Router (R1)
- Cisco ASA 5505 Firewall (FW1)

### Network Design

In the New York Branch, both user workstations are connected to a Layer 2 switch. The switch connects directly to the branch router, which then connects to a firewall before reaching the internet.

Traffic Flow:

```text
PC → Switch → Router → Firewall → Internet
```

This design places the firewall on the external side of the router, allowing the router to handle internal network traffic before traffic is inspected by the firewall and forwarded to the internet.

The New York Branch primarily represents a user-focused environment where employees access company resources and communicate with external networks.

---

## Tokyo Branch

The Tokyo Branch represents a server-focused environment containing critical business resources.

### Devices

- Server 1
- Server 2
- Cisco 2960 Switch (SW2)
- Cisco ASA 5505 Firewall (FW2)
- Cisco 2911 Router (R2)

### Network Design

In the Tokyo Branch, both servers connect to a Layer 2 switch. The switch connects directly to the firewall, which then connects to the router before reaching the internet.

Traffic Flow:

```text
Server → Switch → Firewall → Router → Internet
```

Unlike the New York Branch, the firewall is positioned internally between the servers and the router.

This design places security controls closer to critical resources, allowing traffic to be inspected before reaching the routing infrastructure.

The Tokyo Branch demonstrates how different locations can implement different security architectures based on operational requirements and the sensitivity of the systems being protected.

---

## Internet Connectivity

The center router labeled "The Internet" represents the public internet or a wide area network (WAN) connection between both branch offices.

This device serves as the communication path that allows traffic to travel between:

- New York Branch
- Tokyo Branch
- External Networks

The internet router acts as the bridge between geographically separated locations.

---

## Attacker System

An additional laptop labeled "Attacker" is connected to the internet.

### Purpose

The attacker device represents an external threat actor attempting to reach internal network resources through the internet.

This system was included to visualize:

- External threats
- Network perimeter security
- The importance of firewalls
- Traffic inspection and filtering

The attacker demonstrates why organizations implement firewalls and layered security controls to protect users, servers, and sensitive data from unauthorized access.

---

## Device Roles

### Routers

Routers connect separate networks together and determine the best path for traffic to travel between locations.

### Switches

Switches provide local connectivity between devices within the same network and forward traffic based on MAC addresses.

### Firewalls

Firewalls inspect and filter network traffic between trusted and untrusted networks while enforcing security policies.

### Endpoints

PCs represent employee workstations used to access company resources.

### Servers

Servers provide services, applications, and resources to users across the organization.

---

## Key Concepts Demonstrated

- Enterprise Network Topology
- Branch Office Connectivity
- Router Functions
- Switch Functions
- Firewall Placement
- Network Segmentation
- WAN Connectivity
- Traffic Flow
- Perimeter Security
- Defense in Depth

---

## What I Learned

This lab helped reinforce the purpose of core networking devices and how they work together in an enterprise environment.

One of the most interesting observations was seeing how the New York and Tokyo branches use different firewall placements to achieve security objectives while still maintaining connectivity to the internet.

Understanding where devices are placed within a network is just as important as understanding what the devices do.

This lab serves as a foundation for future networking concepts including:

- VLANs
- Trunking
- Routing
- OSPF
- ACLs
- Network Security
- Enterprise Network Design

---

## Skills Practiced

- Network Device Identification
- Enterprise Topology Analysis
- Router Fundamentals
- Switching Fundamentals
- Firewall Fundamentals
- Traffic Flow Analysis
- Security Architecture Concepts
- Cisco Packet Tracer
