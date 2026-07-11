# IP Addressing

## Addressing Philosophy

Infrastructure and servers receive reserved addresses. User devices remain DHCP.
Each VLAN will receive its own /24 subnet following the 10.10.x.0/24 convention.

## LAN
...
## LAN

Subnet: 10.10.10.0/24
Gateway: 10.10.10.1
DHCP: 10.10.10.100 - 10.10.10.254
DNS: 10.10.10.1

Infrastructure

| Device      | IP Address  | Assignment        | VLAN |      Notes    |
|-------------|-------------|-------------------|      |---------------|
| UCG Fiber   | 10.10.10.1  | Static            |      | Gateway       |
| USW Flex    | 10.10.10.122| DHCP Reservation  |      |               |
| U6 Pro      | 10.10.10.130| DHCP Reservation  |      |               |
| U7 Pro      | 10.10.10.131| DHCP Reservation  |      |               |
| Infra01     | 10.10.10.163| DHCP Reservation  |      | Raspberry Pi  |
| TrueNAS     | 10.10.10.254| DHCP Reservation  |      | Apps Host     |

## Reserved Ranges

These ranges are reserved for planning purposes and should be used when assigning new devices.

- **10.10.10.2 - 10.10.10.49** — Reserved Infrastructure
- **10.10.10.50 - 10.10.10.99** — Servers / Hypervisors
- **10.10.10.100 - 10.10.10.199** — DHCP Clients
- **10.10.10.200 - 10.10.10.254** — DHCP Reservations