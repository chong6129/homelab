# Network Topology

```text
Bell Canada
    │ 10 GbE WAN (UCG SFP+ port 7)
    ▼
UniFi UCG Fiber — 10.10.10.1
    ├── port 3, 2.5 GbE
    │   └── USW Flex 2.5G 8 PoE — 10.10.10.216 (uplink port 8)
    │       ├── port 2, 1 GbE/PoE   → U6 Pro — 10.10.10.130
    │       ├── port 5, 2.5 GbE/PoE → U7 Pro — 10.10.10.131
    │       ├── port 6, 1 GbE       → Proxmox pve-3050 — 10.10.10.2
    │       │                           native VLAN 1; all tagged VLANs allowed
    │       ├── port 7, 1 GbE       → Infra02 — 10.10.10.189
    │       └── port 9, 2.5 GbE     → USW Flex 2.5G 5 — 10.10.10.122
    │                                   └── port 1 → trusted desktop
    └── SFP+ port 6, 10 GbE DAC
        └── TrueNAS SCALE — 10.10.10.254 (single interface)
```

The Proxmox switch port uses Default (VLAN 1) as its native network and allows
all tagged VLANs. This carries Security-Lab VLAN 66 to the Kali and Windows
sandbox guests. All other connected switch ports also use Default as their
native network.

The former US 8 PoE 150W switch is offline and is not part of the active path.
Serial numbers and the public WAN address are intentionally omitted.
