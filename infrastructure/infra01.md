# Infra01

## Overview

| Item | Value |
|------|-------|
| Hostname | Infra01 |
| Hardware | Raspberry Pi 5 (8 GB) |
| OS | Debian GNU/Linux 13 (trixie) |
| Kernel | 6.18.34+rpt-rpi-2712 |
| IP Address | 10.10.10.163 |
| Storage | 64 GB SanDisk Ultra microSD |
| Purpose | Core access and infrastructure services |

## Current Role

Infra01 hosts the services used to reach and manage the homelab:

- Authentik and PostgreSQL
- Homepage
- Uptime Kuma
- Portainer
- Node Exporter

The centralized monitoring workload was migrated to Infra02 to move
write-intensive Prometheus and Grafana data off Infra01's microSD storage.

## Build Log

### 2026-07-06

- Installed Debian 13 (Trixie)
- Enabled SSH
- Updated all packages
- Installed base administration tools
- Installed Docker (pending)
- Installed Docker Engine
- Installed Docker Compose Plugin
- Created /opt/docker directory
- Created homelab Docker network
