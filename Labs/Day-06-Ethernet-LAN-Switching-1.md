# Day 06 - Ethernet LAN Switching & MAC Address Tables

## Overview

This lab was completed as part of my CCNA studies using Cisco Packet Tracer.

The focus of this lab was understanding how Ethernet LAN switching works inside a local network. I used multiple PCs connected across two switches to observe how switches learn MAC addresses, forward frames, flood unknown traffic, and update their MAC address tables dynamically.

This lab helped reinforce one of the most important switching concepts in networking: switches make forwarding decisions based on MAC addresses, not IP addresses.

---

## Network Topology

<p align="center">
  <a href="PASTE-IMAGE-LINK-1-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-06-Ethernet-LAN-Switching-1.png" alt="Day 06 Ethernet LAN Switching Topology">
  </a>
</p>

<p align="center">
  <em>Initial topology showing four PCs connected across two Cisco switches on the 192.168.1.0/24 network.</em>
</p>

---

## Lab Objective

The objective of this lab was to:

- Observe Ethernet switching behavior
- Generate traffic using ICMP ping
- Analyze ARP and ICMP traffic in simulation mode
- Understand how switches learn MAC addresses
- View dynamic MAC address table entries
- Clear dynamic MAC address entries from the switches
- Observe how traffic is handled when MAC address tables are empty

---

## Devices Used

### Switches

- Cisco 2960 Switch SW1
- Cisco 2960 Switch SW2

### End Devices

- PC1
- PC2
- PC3
- PC4

### Network

```text
192.168.1.0/24
```

---

## Lab Scenario

Both switches started with empty MAC address tables, and all PCs started with empty ARP tables.

The main scenario tested was:

```text
PC1 pings PC3
```

Since PC1 did not initially know PC3's MAC address, it first had to use ARP to discover the destination MAC address.

After ARP completed, ICMP traffic was able to travel successfully between PC1 and PC3.

---

## Step 1 - Initial Topology and Lab Instructions

<p align="center">
  <a href="PASTE-IMAGE-LINK-1-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-06-Ethernet-LAN-Switching-1.png">
  </a>
</p>

<p align="center">
  <em>The lab begins with four PCs connected across two switches. The switches start with empty MAC address tables, and the PCs start with empty ARP tables.</em>
</p>

### Topology Summary

- PC1 and PC2 are connected to SW1
- PC3 and PC4 are connected to SW2
- SW1 and SW2 are connected together using a switch-to-switch link
- All devices are part of the same local network

This setup allowed me to observe how frames move across switches within the same broadcast domain.

---

## Step 2 - Ping Test and Packet Simulation

<p align="center">
  <a href="PASTE-IMAGE-LINK-2-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-06-Ethernet-LAN-Switching-2.png" alt="Ping test and Packet Tracer simulation mode">
  </a>
</p>

<p align="center">
  <em>PC1 successfully pings PC3 while Packet Tracer simulation mode shows ARP and ICMP traffic moving through the network.</em>
</p>

### What Happened

PC1 sent a ping to PC3:

```text
ping 192.168.1.3
```

The ping was successful with zero packet loss.

Before ICMP communication could complete, PC1 had to resolve PC3's MAC address using ARP.

### ARP Behavior Observed

The ARP request was sent as a broadcast frame:

```text
Destination MAC: FFFF.FFFF.FFFF
```

Because the destination MAC address was unknown, the switch flooded the frame out all ports except the port it was received on.

Once PC3 received the ARP request, it responded with its MAC address. After that, PC1 could send ICMP traffic directly to PC3.

---

## Step 3 - MAC Address Table Verification

<p align="center">
  <a href="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-06-Ethernet-LAN-Switching-3.png">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-06-Ethernet-LAN-Switching-3.png" alt="MAC address table verification on SW1 and SW2">
  </a>
</p>

<p align="center">
  <em>SW1 and SW2 display dynamically learned MAC addresses after traffic was generated across the network.</em>
</p>

### Command Used

```cisco
show mac address-table
```

### What I Observed

After generating traffic, both switches dynamically learned MAC addresses from connected devices.

The MAC address table showed:

- VLAN information
- MAC addresses
- Entry type
- Associated switch ports

Example:

```text
Type: Dynamic
```

This confirmed that the switches were learning MAC addresses automatically based on the source MAC address of incoming frames.

---

## Step 4 - Clearing Dynamic MAC Address Entries

<p align="center">
  <a href="PASTE-IMAGE-LINK-4-HERE">
    <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-06-Ethernet-LAN-Switching-4.png" alt="Clearing dynamic MAC address tables on switches">
  </a>
</p>

<p align="center">
  <em>Dynamic MAC address entries were cleared from both switches to observe how the switches behave when their MAC address tables are empty again.</em>
</p>

### Commands Used

```cisco
clear mac address-table dynamic
```

```cisco
show mac address-table
```

### What Happened

After clearing the dynamic MAC address table, the switches no longer had learned entries for the PCs.

This allowed the lab to demonstrate what happens when a switch receives traffic and does not yet know the destination MAC address.

In that case, the switch floods the frame until it learns where the destination device is located.

---

## Key Concepts Learned

### MAC Address Learning

Switches learn MAC addresses by examining the source MAC address of incoming Ethernet frames.

### Frame Forwarding

If the destination MAC address is known, the switch forwards the frame only out the correct port.

### Frame Flooding

If the destination MAC address is unknown, the switch floods the frame out all ports except the port where the frame entered.

### ARP

ARP is used to map an IPv4 address to a MAC address.

Before PC1 could communicate with PC3, it needed to discover PC3's MAC address.

### Broadcast Frames

ARP requests use a broadcast destination MAC address:

```text
FFFF.FFFF.FFFF
```

This allows all devices in the local network to receive the request.

---

## Commands Practiced

```cisco
show mac address-table
```

```cisco
clear mac address-table dynamic
```

```text
ping 192.168.1.3
```

---

## Skills Practiced

- Ethernet LAN Switching
- MAC Address Table Analysis
- ARP Traffic Observation
- ICMP Testing
- Frame Forwarding
- Frame Flooding
- Packet Tracer Simulation Mode
- Cisco Switch Verification Commands
- Basic Layer 2 Troubleshooting

---

## What I Learned

This lab helped me understand how switches build and maintain MAC address tables.

The biggest takeaway was seeing how a switch behaves differently depending on whether it knows the destination MAC address.

If the MAC address is known, the frame is forwarded directly.

If the MAC address is unknown, the frame is flooded.

Watching ARP and ICMP traffic in Packet Tracer simulation mode made Ethernet switching much easier to understand because I could see the traffic move through the network step by step.

This lab reinforced important Layer 2 concepts that are foundational for future CCNA topics including VLANs, trunking, spanning tree, EtherChannel, and network troubleshooting.

---

## Lab Status

✅ Day 06 Complete

### Topics Covered

- Ethernet LAN Switching
- MAC Address Learning
- MAC Address Tables
- ARP
- ICMP
- Frame Flooding
- Frame Forwarding
- Switch Verification Commands

### Next Topic

➡️ Day 07 - IPv4 Addressing and Subnetting
