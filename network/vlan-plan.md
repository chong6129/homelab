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

## VLAN 60 - Lab

Purpose
- Kali
- Windows test VM
- Ubuntu test VM

Notes
This VLAN is intentionally unrestricted for testing.