# Firewall Rules

Inter-VLAN policy is enforced by the UniFi gateway. The Security Lab follows a
default-deny model toward trusted and infrastructure networks.

## Security Lab Management Exception

| Setting | Value |
|---|---|
| Action | Allow |
| Protocol | TCP |
| Source | Trusted desktop `10.10.10.115` |
| Destination | `winSB` `10.66.6.115` |
| Destination port | `3389` (RDP) |
| Direction | Trusted LAN to VLAN 66 |
| Purpose | Restricted administration of the Windows sandbox |

UniFi has **Auto Allow Return Traffic** enabled for this policy. It generates a
stateful companion rule from source `10.66.6.115:3389` back to destination
`10.10.10.115`; this is not a second unrestricted management exception.

This exception is intentionally host-specific and service-specific. It does
not permit general trusted-LAN access to VLAN 66, and it does not allow VLAN 66
to initiate connections to the trusted LAN.

## Security Lab Baseline

- VLAN 66 to VLAN 66: allowed for controlled lab activity.
- VLAN 66 to the internet: allowed for tools, updates, and NTP.
- VLAN 66 to trusted and infrastructure networks: blocked.
- Trusted and infrastructure networks to VLAN 66: blocked unless covered by
  the RDP management exception above.

The exception and the surrounding isolation policy have both been tested.

## Gateway Management Protection

`Security-Lab - Block Gateway Management` blocks all IPv4 protocols from the
Security-Lab network to the Gateway zone. It applies at all times and has
syslog logging enabled.
