# IP Addressing

## Active Subnets

| Network | Subnet | Gateway | DHCP range |
|---|---|---|---|
| Default (VLAN 1) | `10.10.10.0/24` | `10.10.10.1` | `10.10.10.100-254` |
| IoT (VLAN 30) | `10.10.30.0/24` | `10.10.30.1` | `10.10.30.100-254` |
| Security-Lab (VLAN 66) | `10.66.6.0/24` | `10.66.6.1` | `10.66.6.6-254` |

## Infrastructure and Lab Systems

| Device | Address | Network | Notes |
|---|---|---|---|
| UCG Fiber | `10.10.10.1` | Default | Gateway |
| Proxmox `pve-3050` | `10.10.10.2` | Default | Hypervisor |
| USW Flex 2.5G 5 | `10.10.10.122` | Default | Access switch |
| U6 Pro | `10.10.10.130` | Default | Access point |
| U7 Pro | `10.10.10.131` | Default | Access point |
| Infra01 | `10.10.10.163` | Default | Core services |
| Infra02 | `10.10.10.189` | Default | Monitoring |
| USW Flex 2.5G 8 PoE | `10.10.10.216` | Default | Core access switch |
| TrueNAS | `10.10.10.254` | Default | Storage and apps |
| Trusted desktop | `10.10.10.115` | Default | RDP management source |
| `winSB` | `10.66.6.115` | Security-Lab | Windows sandbox |
| Kali | `10.66.6.122` | Security-Lab | Security workstation |

The UniFi label that previously identified `10.10.10.189` as Home Assistant was
stale. That Home Assistant instance has been retired; the address belongs to
Infra02. Home Assistant may be redeployed later.
