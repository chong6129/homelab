# UniFi Networks and VLANs

This file records the networks currently deployed on the UniFi UCG Fiber.
Future or conceptual VLANs are not listed as active infrastructure.

| Network | VLAN ID | Subnet | Gateway | DHCP range | DNS | Isolation |
|---|---:|---|---|---|---|---|
| Default | 1 | `10.10.10.0/24` | `10.10.10.1` | `10.10.10.100-254` | Automatic | None |
| IoT | 30 | `10.10.30.0/24` | `10.10.30.1` | `10.10.30.100-254` | Automatic | Not currently enabled |
| Security-Lab | 66 | `10.66.6.0/24` | `10.66.6.1` | `10.66.6.6-254` | `1.1.1.1`, `1.0.0.1` | Enabled |

All three networks permit internet access and use a 86,400-second DHCP lease.
Ping conflict detection is enabled. Security-Lab additionally uses DHCP
guarding with `10.66.6.1` as its trusted DHCP server.

## Security-Lab

The lab contains Kali (`10.66.6.122`) and `winSB` (`10.66.6.115`). UniFi's
isolated-network policy prevents VLAN 66 from initiating traffic to trusted
internal networks. A separate gateway-management rule prevents VLAN 66 from
accessing the UCG Fiber management plane.

The only custom management path is TCP/3389 from trusted desktop
`10.10.10.115` to `winSB`; UniFi automatically permits the stateful return
traffic.

## IoT Status

IoT is a dedicated VLAN and SSID, but network isolation is currently disabled.
It must not be described as isolated until an isolation policy is deployed and
tested.
