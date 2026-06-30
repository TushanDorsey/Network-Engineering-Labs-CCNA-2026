# CCNA Lab Day 11.2 - Troubleshooting Static Routes

## 📖 Overview

In this lab, I shifted from configuring routers to troubleshooting an existing network. Each router contained a single misconfiguration that prevented communication between PC1 and PC2. Using a systematic troubleshooting methodology, I identified routing and interface issues, corrected the configurations, and restored full end-to-end connectivity.

---

# 🖥️ Network Topology

<img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2011.2%20Lab%20-%20Troubleshooting%20Static%20Routes.png" width="1000"/>

---

# 📸 Lab Walkthrough

## Initial Network State

The network was intentionally misconfigured. PC1 and PC2 could not communicate across the routed network.

---

## Step 1 — Verify End-to-End Connectivity

<img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2011.2%20Lab%20-%20Troubleshooting%20Static%20Routes-6.png" width="1000"/>

Confirmed the issue by attempting to ping the remote network. The ping failed, indicating a Layer 3 routing problem.

---

## Step 2 — Inspect Router R1

<img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2011.2%20Lab%20-%20Troubleshooting%20Static%20Routes-2.1.png" width="1000"/>

Reviewed R1's interface configuration and routing table.

<img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2011.2%20Lab%20-%20Troubleshooting%20Static%20Routes-2.2.png" width="1000"/>

Discovered an incorrect static route pointing toward the wrong destination.

---

## Step 3 — Inspect Router R2

<img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2011.2%20Lab%20-%20Troubleshooting%20Static%20Routes-3.1.png" width="1000"/>

Verified interface status and reviewed the routing table.

<img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2011.2%20Lab%20-%20Troubleshooting%20Static%20Routes-3.2.png" width="1000"/>

Removed the incorrect static route and configured the proper route toward the destination network.

---

## Step 4 — Inspect Router R3

<img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2011.2%20Lab%20-%20Troubleshooting%20Static%20Routes-4.1.png" width="1000"/>

Verified interface addressing and routing information.

<img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2011.2%20Lab%20-%20Troubleshooting%20Static%20Routes-4.2.png"/>

Found an incorrect interface IP address that prevented proper routing. Corrected the interface configuration and verified the changes.

---

## Step 5 — Final Connectivity Test

<img src="https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day%2011.2%20Lab%20-%20Troubleshooting%20Static%20Routes-5.png" width="1000"/>

Performed end-to-end ping testing after all corrections were made.

✔️ PC1 successfully communicated with PC2.

✔️ Static routing was fully operational.

✔️ End-to-end connectivity was restored.

---

# 🛠️ Commands Used

```bash
show ip interface brief

show ip route

configure terminal

interface g0/0

interface g0/1

ip address

ip route

no ip route

ping
```

---

# 📚 Skills Practiced

- Static Route Troubleshooting
- Cisco IOS CLI
- Layer 3 Routing
- Route Verification
- Routing Table Analysis
- Interface Verification
- ICMP Connectivity Testing
- Root Cause Analysis
- Enterprise Troubleshooting Methodology
- Network Documentation

---

# 🎯 Key Takeaways

Rather than simply configuring a network, this lab focused on troubleshooting existing infrastructure by following a structured process:

- Verify connectivity
- Inspect interfaces
- Analyze routing tables
- Identify misconfigurations
- Correct routing information
- Validate connectivity

This mirrors the real-world workflow of Network Engineers who spend much of their time diagnosing and resolving production network issues rather than building networks from scratch.

---

## 🚀 What's Next

Day 12 continues expanding on routing concepts and prepares for more advanced network troubleshooting scenarios as I continue my CCNA journey.
