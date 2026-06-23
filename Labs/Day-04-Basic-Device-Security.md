# Day 04 - Basic Device Security & Cisco IOS Administration

## Overview

This lab focused on securing Cisco network devices using fundamental Cisco IOS security configurations.

The objective was to learn how network administrators and engineers secure routers and switches by implementing hostnames, enable passwords, enable secrets, password encryption, and configuration management practices.

While the topology itself is simple, the concepts introduced in this lab are used daily in production environments and serve as the foundation for more advanced topics such as SSH, AAA, TACACS+, RADIUS, and network device hardening.

---

## Network Topology

### Lab Topology

The lab environment consists of:

* Cisco 2911 Router (R1)
* Cisco 2960 Switch (SW1)
* Three End User PCs

  * PC1
  * PC2
  * PC3

The router and switch act as the network infrastructure devices being secured, while the PCs represent end-user devices connected to the network.

---

## Lab Objectives

The goals of this lab were to:

* Configure device hostnames
* Configure privileged EXEC passwords
* Configure encrypted administrative credentials
* Enable password encryption services
* Verify security configurations
* Save configurations to startup memory
* Understand the difference between enable password and enable secret

---

# Step 1 - Configure Device Hostnames

### Router Hostname Configuration

![Router Hostname Configuration](https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-04-Basic-Device-Security-1.png)

The first task was to assign meaningful hostnames to both devices.

Router:

```cisco
hostname R1
```

Switch:

```cisco
hostname SW1
```

Using descriptive hostnames makes device identification easier when managing multiple network devices in an enterprise environment.

---

# Step 2 - Configure Enable Password

### Enable Password Configuration

![Enable Password Configuration](https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-04-Basic-Device-Security-2.png)

An enable password was configured to control access to Privileged EXEC mode.

```cisco
enable password CCNA
```

This password provides administrative access but is considered less secure because it can be viewed in certain configurations if encryption is not enabled.

---

# Step 3 - Verify Running Configuration

### Initial Configuration Verification

![Running Configuration Verification](https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-04-Basic-Device-Security-3.png)

The running configuration was reviewed using:

```cisco
show running-config
```

At this stage, the enable password was visible within the configuration.

This demonstrates why additional security measures are required to protect administrative credentials.

---

# Step 4 - Configure Password Encryption

### Service Password Encryption

![Password Encryption](https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-04-Basic-Device-Security-4.png)

Password encryption was enabled using:

```cisco
service password-encryption
```

This command encrypts passwords stored within the running configuration, preventing them from appearing in plain text.

This is an important security practice used to protect administrative credentials.

---

# Step 5 - Configure Enable Secret

### Enable Secret Configuration

![Enable Secret Configuration](https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-04-Basic-Device-Security-4.png)

A secure administrative password was configured using:

```cisco
enable secret Cisco
```

The enable secret is preferred over the enable password because it uses stronger encryption methods.

In production environments, network engineers typically rely on enable secrets rather than standard enable passwords.

---

# Step 6 - Configure Switch Security

### Switch Configuration

![Switch Configuration](https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Lab-Photos/Day-04-Basic-Device-Security-5.png)

The same security principles were applied to the Cisco switch.

Configured:

* Hostname
* Enable Password
* Enable Secret
* Password Encryption

This ensures security standards are consistently applied across all network infrastructure devices.

---

# Step 7 - Final Verification

### Final Running Configuration

![Final Verification](PASTE-FINAL-VERIFICATION-IMAGE-LINK-HERE)

The final running configuration was reviewed to confirm:

* Hostname changes
* Encrypted credentials
* Enable password configuration
* Enable secret configuration
* Password encryption settings

Commands Used:

```cisco
show running-config
```

```cisco
write memory
```

or

```cisco
copy running-config startup-config
```

This ensures the configuration is preserved after a reboot.

---

# Security Concepts Learned

## Enable Password

Provides access to privileged EXEC mode.

Example:

```cisco
enable password CCNA
```

Less secure than an enable secret.

---

## Enable Secret

Provides secure administrative access.

Example:

```cisco
enable secret Cisco
```

Uses stronger encryption and overrides the enable password.

---

## Service Password Encryption

Encrypts plaintext passwords stored in the configuration.

Example:

```cisco
service password-encryption
```

Helps prevent credentials from being exposed when viewing configuration files.

---

# Skills Practiced

* Cisco IOS Navigation
* Router Administration
* Switch Administration
* Password Security
* Device Hardening Fundamentals
* Configuration Verification
* Running Configuration Analysis
* Startup Configuration Management
* Basic Cisco Security Practices

---

# Key Takeaways

This lab introduced several foundational security practices used by network administrators and engineers.

The most important lesson learned was understanding the difference between:

* Enable Password
* Enable Secret

and why encrypted credentials should always be used whenever possible.

Securing administrative access is one of the first steps in protecting network infrastructure and serves as the foundation for more advanced security implementations later in the CCNA curriculum.

---

## Lab Status

✅ Day 04 Complete

### Topics Covered

* Hostnames
* Enable Passwords
* Enable Secrets
* Password Encryption
* Cisco IOS Administration
* Configuration Management
* Device Security Fundamentals

### Next Topic

➡️ Day 05 - IPv4 Addressing & Subnetting

