# IP Addressing

## Active Subnets

The subnet table is the September baseline. Nextcloud's confirmed address is
`10.10.50.50`; its prefix, gateway, DHCP range, and VLAN ID remain to be
inventoried, so no subnet row is inferred from that address alone.

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
| DC01 | `10.66.6.10` | Security-Lab | Server 2025 AD, `corp.longn.ca` |
| DC02 | `10.66.6.11` | Security-Lab | Server 2025 AD, `corp.longn.ca` |
| Nextcloud Ubuntu VM | `10.10.50.50` | VLAN/subnet details pending | Docker stack and NFS client |

WIN11-01 is domain-joined; its address is not recorded in the available notes.
DC addresses fall inside the previously documented lab DHCP range; exclusion
or reservation details need verification alongside the current network inventory.

The UniFi label that previously identified `10.10.10.189` as Home Assistant was
stale. The monitoring deployment assigned this address to Infra02. Home
Assistant is in use again in the October dashboard notes, but its current
host/IP was not captured; do not reuse the stale label as address evidence.

The Paperless continuation notes identify Ollama at `10.10.10.249:11434`.
November Jellyfin troubleshooting reports internal resolution to `10.10.10.4`;
the Caddy host role needs reconciliation before adding a definitive inventory row.
