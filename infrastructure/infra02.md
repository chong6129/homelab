# Infra02

## Overview

| Item | Value |
|---|---|
| Hostname | Infra02 |
| Hardware | Raspberry Pi 5 (8 GB) |
| Operating system | Debian GNU/Linux 13 (Trixie), ARM64 |
| IP address | 10.10.10.189 |
| Storage | 256 GB Patriot P320 NVMe on an M.2 HAT |
| Root filesystem | NVMe (`/dev/nvme0n1p2`) |
| Purpose | Observability and operations |

## Current Services

- Prometheus
- Grafana
- Prometheus PVE Exporter
- Prometheus Graphite Exporter
- Node Exporter

## Monitoring Role

Infra02 owns the centralized monitoring stack and its persistent data. The
deployment monitors Infra01, Infra02, Proxmox, and TrueNAS.

```text
TrueNAS 10.10.10.254
        │ Graphite telemetry on TCP/9109
        ▼
Infra02 10.10.10.189
        ├── Graphite Exporter
        ├── PVE Exporter ──► Proxmox 10.10.10.2
        ├── Prometheus
        ├── Grafana
        └── Node Exporter
```

Grafana remains available through `grafana.longn.ca`. External requests pass
through Cloudflare and Authentik on Infra01 before Authentik proxies them to
Grafana on Infra02 at TCP/3005.

The migration from Infra01 preserved the existing Grafana configuration,
dashboards, Prometheus history, and TrueNAS Graphite telemetry. Infra01 now
runs only Node Exporter from its former monitoring stack.
