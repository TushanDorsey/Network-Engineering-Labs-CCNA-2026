# Day 02 - Connecting Network Devices

## Overview

This lab was completed as part of my CCNA studies using Cisco Packet Tracer.

The objective of this exercise was to correctly connect network devices using the appropriate cabling standards based on device type, connection type, and transmission distance.

This lab reinforced the importance of understanding when to use straight-through cables, crossover cables, single-mode fiber, and multi-mode fiber within an enterprise network environment.

---

## Lab Objective

Design and connect a multi-site enterprise network using the proper cable type for each connection.

Topics covered:

* Copper Ethernet Cabling
* Straight-Through Connections
* Crossover Connections
* Single-Mode Fiber
* Multi-Mode Fiber
* Router Connectivity
* Switch Connectivity
* End Device Connectivity
* Network Topology Design

---

## Network Topology

<p align="center">
  <img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-02-Connecting-Devices.png" alt="Day 01 Network Devices Lab" width="1000">
</p>

---

## Site A Network

The left side of the topology represents a branch office environment.

### Devices

* Router R1
* Router R2
* Switch SW1
* Switch SW2
* Switch SW3
* Switch SW4
* PC1
* PC2

### Cabling Used

#### Straight-Through Connections

Used between unlike devices:

* R2 → SW1
* R2 → SW2
* SW3 → PC1
* SW4 → PC2

#### Crossover Connections

Used between like devices:

* SW1 → SW2
* SW1 → SW3
* SW2 → SW4

### Purpose

This section demonstrates proper Ethernet cabling standards for connecting routers, switches, and end-user devices within a local area network.

---

## Site B Network

The right side of the topology represents a second branch location.

### Devices

* Router R3
* Router R4
* Switch SW5
* Switch SW6
* Switch SW7
* Switch SW8
* PC3
* Server SRV1

### Cabling Used

#### Straight-Through Connections

* R4 → SW5
* R4 → SW6
* SW7 → PC3
* SW8 → Server

#### Crossover Connections

* SW5 → SW6
* SW5 → SW7
* SW6 → SW8

### Purpose

This branch demonstrates how user devices and servers can coexist within the same network while maintaining proper connectivity through switching infrastructure.

---

## Router-to-Router Connectivity

### R1 to R2

Distance: 50 meters

Cable Used:

* Straight-Through STP/UTP

Reason:

The short distance allows copper cabling to provide reliable communication between routers.

---

### R3 to R4

Distance: 250 meters

Cable Used:

* Multi-Mode Fiber

Reason:

Multi-mode fiber supports higher bandwidth over medium distances and is commonly used for building-to-building connectivity.

---

### R1 to R3

Distance: 3 Kilometers

Cable Used:

* Single-Mode Fiber

Reason:

Single-mode fiber is designed for long-distance communication and is capable of transmitting data over several kilometers with minimal signal loss.

---

## Cable Selection Summary

| Connection Type             | Cable Used        |
| --------------------------- | ----------------- |
| Router to Switch            | Straight-Through  |
| Switch to PC                | Straight-Through  |
| Switch to Server            | Straight-Through  |
| Switch to Switch            | Crossover         |
| Short Router Connections    | Copper Ethernet   |
| Medium Distance Connections | Multi-Mode Fiber  |
| Long Distance Connections   | Single-Mode Fiber |

---

## Key Concepts Learned

### Straight-Through Cables

Used to connect unlike devices:

* Router to Switch
* Switch to PC
* Switch to Server

### Crossover Cables

Used to connect like devices:

* Switch to Switch

### Single-Mode Fiber

Designed for long-distance communication.

Commonly used by:

* Internet Service Providers
* Telecommunications Providers
* Enterprise WAN Connections

### Multi-Mode Fiber

Designed for medium-distance communication.

Commonly used for:

* Campus Networks
* Building Interconnections
* Data Centers

---

## Skills Practiced

* Physical Layer Concepts
* Cable Identification
* Ethernet Standards
* Fiber Optic Selection
* Enterprise Topology Design
* Router Connectivity
* Switch Connectivity
* Network Infrastructure Planning
* Cisco Packet Tracer

---

## What I Learned

This lab reinforced that selecting the correct cable is just as important as configuring the network itself.

Different network devices require different cabling standards, and transmission distance directly impacts whether copper, multi-mode fiber, or single-mode fiber should be used.

Understanding these physical layer concepts provides the foundation for more advanced networking topics such as VLANs, routing, switching, and enterprise network design.
