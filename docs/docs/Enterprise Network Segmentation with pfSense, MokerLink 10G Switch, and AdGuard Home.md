# Enterprise Network Segmentation with pfSense, MokerLink 10G Switch, and AdGuard Home

![Status](https://img.shields.io/badge/Status-Completed-success)
![Platform](https://img.shields.io/badge/Platform-pfSense-blue)
![Switch](https://img.shields.io/badge/Switch-MokerLink%2010G-orange)
![DNS](https://img.shields.io/badge/DNS-AdGuard%20Home-green)
![License](https://img.shields.io/badge/License-MIT-lightgrey)

---

# Overview

This project demonstrates the design and implementation of a segmented enterprise-style home lab network using:

- pfSense Plus
- MokerLink 10GT080M Managed 10G Switch
- AdGuard Home DNS Filtering
- Proxmox Virtualization
- VLAN Segmentation
- Inter-VLAN Firewall Controls
- Management Network Isolation
- DNS Centralization

The goal was to move from a flat network to a security-focused architecture that mirrors real-world enterprise environments.

---

# Project Goals

## Security

Separate device categories into dedicated VLANs.

Examples:

- Trusted Devices
- IoT Devices
- Guest Devices
- Servers
- Management Systems
- Lab/Test Environment

---

## Management

Create a dedicated management network for:

- Firewall administration
- Switch administration
- Hypervisor administration
- Access Point administration

---

## Visibility

Centralize DNS through AdGuard Home for:

- DNS filtering
- Logging
- Malware domain blocking
- Ad blocking

---

## Enterprise Learning

Develop practical experience with:

- VLAN Design
- Layer 2 Switching
- Inter-VLAN Routing
- Firewall Policy Design
- Infrastructure Documentation

---

# Network Architecture

```text
                 Internet
                     │
                     │
               ┌──────────┐
               │ pfSense  │
               └────┬─────┘
                    │
               Trunk Port
                    │
          ┌─────────────────┐
          │ MokerLink 10G   │
          │ Managed Switch  │
          └─────────────────┘
                    │
      ┌─────────────┼─────────────┐
      │             │             │
   Proxmox        AP         Workstations
```

---

# VLAN Design

| VLAN | Name | Purpose |
|--------|---------|----------|
| 10 | ORION | Trusted Devices |
| 20 | ANDROMEDA | IoT Devices |
| 30 | ATLAS | Lab Environment |
| 40 | PEGASUS | Guest Devices |
| 50 | CASSIOPEIA | Servers |
| 99 | DRACO | Management |
| 999 | BLACKHOLE | Unused Ports |

---

# VLAN Purpose

## ORION

Trusted devices such as:

- Primary workstation
- Personal devices
- Administrative jump box

---

## ANDROMEDA

IoT devices:

- Cameras
- Smart Home Devices
- Streaming Devices

---

## ATLAS

Lab systems:

- Test VMs
- Sandbox environments
- Security experimentation

---

## PEGASUS

Guest Wi-Fi network.

Provides:

- Internet access only
- No internal network access

---

## CASSIOPEIA

Server network.

Examples:

- Domain Controllers
- DNS Servers
- AdGuard Home
- Monitoring Systems
- Linux Servers

---

## DRACO

Management network.

Examples:

- pfSense
- Switch Management
- Access Point Management
- Proxmox Management

---

## BLACKHOLE

Unused switch ports.

Purpose:

- Prevent unauthorized access
- Prevent accidental device connections

---

# pfSense Configuration

---

# Step 1 – Create VLANs

Navigate to:

```text
Interfaces
└── Assignments
    └── VLANs
```

Select:

```text
Add VLAN
```

Create:

| VLAN ID | Description |
|----------|------------|
| 10 | ORION |
| 20 | ANDROMEDA |
| 30 | ATLAS |
| 40 | PEGASUS |
| 50 | CASSIOPEIA |
| 99 | DRACO |
| 999 | BLACKHOLE |

Save after each VLAN.

---

# Step 2 – Assign Interfaces

Navigate to:

```text
Interfaces
└── Assignments
```

Assign each VLAN as an interface.

Rename:

```text
VLAN10_ORION
VLAN20_ANDROMEDA
VLAN30_ATLAS
VLAN40_PEGASUS
VLAN50_CASSIOPEIA
VLAN99_DRACO
```

Enable each interface.

---

# Step 3 – Configure Gateways

For each VLAN:

```text
Interfaces
└── VLAN Interface
```

Configure:

```text
IPv4 Configuration Type:
Static IPv4
```

Example Sanitized Scheme:

| VLAN | Gateway |
|--------|---------|
| ORION | x.x.10.1 |
| ANDROMEDA | x.x.20.1 |
| ATLAS | x.x.30.1 |
| PEGASUS | x.x.40.1 |
| CASSIOPEIA | x.x.50.1 |
| DRACO | x.x.99.1 |

---

# Step 4 – Configure DHCP

Navigate:

```text
Services
└── DHCP Server
```

Enable DHCP per VLAN.

Configure:

```text
Gateway
DNS Server
Address Pool
```

Use DNS pointing to AdGuard Home.

---

# Step 5 – Create Aliases

Navigate:

```text
Firewall
└── Aliases
```

Create aliases:

## MGMT_HOSTS

Management devices.

Examples:

```text
Firewall
Switch
Access Point
Hypervisor
```

---

## ADMIN_HOSTS

Administrative workstations.

---

## DNS_SERVERS

Centralized DNS servers.

---

# Step 6 – Firewall Rules

General Design:

```text
Block First
Allow Only What Is Required
```

---

## ORION

Allow:

```text
Internet
DNS
Required Internal Services
```

Block:

```text
Management Network
```

Except:

```text
Admin Hosts
```

---

## ANDROMEDA

Allow:

```text
DNS
Internet
```

Block:

```text
Management
Servers
Trusted Devices
```

---

## ATLAS

Allow:

```text
Internet
DNS
```

Block:

```text
Management Infrastructure
```

---

## PEGASUS

Allow:

```text
Internet
DNS
```

Block:

```text
All Internal Networks
```

---

## CASSIOPEIA

Allow:

```text
Necessary Service Traffic
```

Examples:

```text
DNS
LDAP
Kerberos
HTTP
HTTPS
```

---

## DRACO

Allow:

```text
Administrative Access
```

Block:

```text
Everything Else
```

---

# MokerLink Switch Configuration

---

# Step 1 – Create VLANs

Navigate:

```text
Switching
└── VLAN
```

Create:

```text
10
20
30
40
50
99
999
```

Apply changes.

---

# Step 2 – Configure Trunk Port

Port connected to pfSense.

Example:

```text
Port 1
```

Configure:

```text
Tagged:
10
20
30
40
50
99
999
```

PVID:

```text
99
```

---

# Step 3 – Configure Proxmox Port

Port connected to hypervisor.

Example:

```text
Port 2
```

Configure:

```text
Tagged:
10
20
30
40
50
99
```

PVID:
99

---

# Step 4 – Configure Workstation Ports

Trusted workstation ports.

Example:

```text
Port 3
```

Membership:

```text
Untagged VLAN 10
```

PVID:

```text
10
```

---

# Step 5 – Configure Access Point Port

Example:

```text
Port 4
```

Membership:

```text
Tagged:
10
20
30
40
99
```

PVID:

```text
99
```

---

# Step 6 – Configure Unused Ports

Assign:

```text
VLAN 999
```

PVID:

```text
999
```

Remove all other VLAN memberships.

---

# AdGuard Home Configuration

---

# Step 1 – Deploy Server

Install:

```bash
curl -s -S -L https://raw.githubusercontent.com/AdguardTeam/AdGuardHome/master/scripts/install.sh | sh
```

---

# Step 2 – Configure Network

Place AdGuard in:

```text
VLAN 50
CASSIOPEIA
```

Assign:

```text
Static Address
```

---

# Step 3 – Configure Upstream DNS

Examples:

```text
Quad9
Cloudflare
Google
```

---

# Step 4 – Configure Clients

Navigate:

```text
Settings
└── DNS Settings
```

Enable:

```text
DNSSEC
EDNS
Rate Limiting
```

---

# Step 5 – Point VLANs to AdGuard

In pfSense:

```text
Services
└── DHCP Server
```

For every VLAN:

```text
DNS Server = AdGuard Home
```

---

# Validation Tests

---

# DNS Resolution

Windows:

```powershell
nslookup github.com
```

Expected:

```text
Server = AdGuard Home
```

---

# Gateway Testing

```powershell
ping firewall-gateway
```

---

# Internet Connectivity

```powershell
Test-NetConnection github.com -Port 443
```

---

# VLAN Isolation Testing

Attempt communication between VLANs.

Expected:

```text
Blocked unless explicitly allowed.
```

---

# Security Improvements Achieved

✅ Dedicated Management Network

✅ DNS Centralization

✅ Guest Isolation

✅ IoT Isolation

✅ Server Isolation

✅ Enterprise VLAN Design

✅ Reduced Lateral Movement Risk

✅ Firewall-Based Access Control

✅ Blackhole VLAN for Unused Ports

---

# Lessons Learned

1. Flat networks do not scale securely.
2. Management traffic should always be isolated.
3. DNS centralization dramatically improves visibility.
4. Explicit firewall rules are easier to audit than broad allow rules.
5. VLAN segmentation provides security without requiring additional hardware.
6. Documentation is just as important as implementation.

---

# Future Improvements

- Security Onion Deployment
- 802.1X Authentication
- WPA3 Enterprise Wi-Fi
- Certificate-Based Authentication
- Network Access Control
- SIEM Integration
- Automated Configuration Backups
- Infrastructure as Code

---

# Technologies Used

- pfSense Plus
- MokerLink 10GT080M
- AdGuard Home
- Proxmox VE
- VLANs
- DHCP
- DNS
- Firewall Rules
- Layer 2 Switching
- Inter-VLAN Routing

---

# Resume Bullet

Designed and implemented a fully segmented enterprise-style network using pfSense, VLANs, managed switching, and centralized DNS services. Created isolated Trusted, IoT, Lab, Guest, Server, and Management networks with firewall-controlled inter-VLAN routing, DNS filtering through AdGuard Home, and secure management-plane separation, reducing lateral movement risk while improving visibility and operational security.