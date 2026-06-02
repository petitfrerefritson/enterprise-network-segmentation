# Enterprise Network Segmentation

## Overview

This project documents the design and implementation of an enterprise-style segmented network built in a homelab environment using pfSense, Proxmox, Active Directory, and managed switching.

The objective was to improve security, reduce attack surface, separate device trust levels, and provide a scalable foundation for future infrastructure services.

---

## Goals

- Separate trusted and untrusted devices
- Create dedicated management network
- Implement firewall-based segmentation
- Support Active Directory infrastructure
- Prepare environment for Security Onion monitoring
- Improve troubleshooting and operational visibility

---

## VLAN Design


![A network diagram showing segmented VLANs and firewall architecture in a homelab environment with labeled VLANs for trusted devices, IoT, lab, guest, servers, management, and unused sink traffic](diagrams/network-diagram.png)

| VLAN | Name | Purpose |
|--------|--------|--------|
| 10 | Orion | Trusted Devices |
| 20 | Andromeda | IoT Devices |
| 30 | Atlas | Lab Environment |
| 40 | Pegasus | Guest Network |
| 50 | Cassiopeia | Servers |
| 99 | Draco | Management |
| 999 | Blackhole | Unused/Sink VLAN |

---

## Technologies Used

- pfSense Plus
- Proxmox VE
- Dell Precision 7810
- MokerLink Managed Switch
- Access Point
- Active Directory
- AdGuard Home
- VLANs
- DHCP
- DNS
- Firewall Rules

<p align="center">
  <img src="screenshots/Firewall Rules ATLAS.png" width="24%">
  <img src="screenshots/Firewall Rules Draco.png" width="24%">
  <img src="screenshots/Membership Table.png" width="24%">
  <img src="screenshots/Interface Assignments.png" width="24%">
</p>

<p align="center">
  <img src="screenshots/GLNET Devices.png" width="32%">
  <img src="screenshots/GLNET Interfaces.png" width="32%">
  <img src="screenshots/Wireless overview.png" width="32%">
</p>

---

## Lessons Learned

- Importance of management plane separation
- Firewall rule ordering
- VLAN tagging and trunk configuration
- DNS architecture considerations
- Enterprise network design methodology

---

## Future Improvements

- Security Onion deployment
- Centralized logging
- WPA3 Enterprise
- Certificate-based authentication
- Network monitoring and alerting
