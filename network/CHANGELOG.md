# Change Log

## 2026-07-10

- Migrated LAN from 192.168.1.0/24 to 10.10.10.0/24.
- Updated DHCP range to .100-.254.
- Assigned infrastructure reservations.
- Updated Cloudflare Tunnel origins.

## 2026-07-11

### Network

- Created IoT VLAN (30) on UCG Fiber.
- Created dedicated `georgeiot` SSID mapped to VLAN 30.
- Migrated Nest Thermostat to IoT VLAN (10.10.30.246).
- Migrated Eufy HomeBase to IoT VLAN (10.10.30.135).
- Verified Internet connectivity from IoT VLAN.

### Services

- Migrated TrueNAS from 192.168.1.x to DHCP reservation at 10.10.10.254.
- Updated Cloudflare Tunnel origins to new TrueNAS IP.
- Restored external access to:
  - vault.longn.ca
  - immich.longn.ca
  - nc.longn.ca
  - Verified Cloudflare Tunnel functionality after LAN migration.

### Documentation

- Documented IP addressing plan.
- Documented DNS and Cloudflare Tunnel architecture.
- Reserved IP address ranges for future VLAN expansion.
- Updated infrastructure inventory.

### Next Steps

- Migrate Kasa switches to `georgeiot`.
- Create firewall rules for VLAN 30.
- Migrate remaining IoT devices.
- Implement Traefik as internal reverse proxy.