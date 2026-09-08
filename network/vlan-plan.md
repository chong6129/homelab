# HomeLab VLAN Plan

## VLAN 10 - Infrastructure
**Subnet:** 10.10.10.0/24
**Gateway:** 10.10.10.1

Purpose
- Network management
- Hypervisors
- Storage
- Management interfaces

Devices
- UCG Fiber
- Infrastructure Switch
- AP Management
- Proxmox
- TrueNAS
- Raspberry Pi (Infra01)

---

## VLAN 20 - Servers
**Subnet:** 10.10.20.0/24

Services
- Docker Hosts
- Homepage
- Portainer
- Authentik
- Grafana
- Prometheus
- Wazuh
- Paperless
- Joplin

---

## VLAN 30 - IoT

Examples
- Google Home
- Smart plugs
- Smart lights
- TVs

Restrictions
- Internet only
- No access to Infrastructure VLAN
- Limited access to Home Assistant

---

## VLAN 40 - Cameras

Purpose
- Security cameras
- NVR

Restrictions
- No Internet
- Can only talk to NVR

---

## VLAN 50 - Guest

Restrictions
- Internet only
- Client Isolation enabled

---

## VLAN 66 - Security Lab

**Subnet:** 10.66.6.0/24

Purpose
- Kali
- Windows test VM
- Ubuntu test VM

Current systems
- Kali: 10.66.6.122
- winSB: 10.66.6.115

Restrictions
- Isolated from trusted and infrastructure networks
- Cannot initiate connections to the trusted LAN
- Internet access retained for controlled tooling, updates, and NTP
- Explicit management exception: trusted desktop 10.10.10.115 to winSB
  10.66.6.115 on TCP/3389 only
