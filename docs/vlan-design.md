# VLAN Design

## Objective

The goal of the VLAN architecture was to separate devices based on trust level and function.

## VLAN Assignments

| VLAN | Name | Purpose |
|------|------|----------|
| 10 | Orion | Trusted devices |
| 20 | Andromeda | IoT devices |
| 30 | Atlas | Lab systems |
| 40 | Pegasus | Guest access |
| 50 | Cassiopeia | Servers |
| 99 | Draco | Management |
| 999 | Blackhole | Unused/Sink VLAN |

## Security Benefits

- Reduced attack surface
- Improved visibility
- Simplified firewall policy design
- Management plane isolation
