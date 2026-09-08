# Long's Enterprise Homelab

Enterprise-inspired self-hosted infrastructure built for hands-on experience with **networking, cybersecurity, virtualization, storage, automation, observability, and local AI/ML**.

The lab is designed as more than a collection of self-hosted applications. It provides an environment for experimenting with infrastructure architecture, network segmentation, identity, secure remote access, GPU workloads, storage design, and operational monitoring.

---

## Architecture

The environment is distributed across four primary compute platforms:

| Platform | Role |
|---|---|
| Raspberry Pi 5 — Infra01 | Core infrastructure and management services |
| Raspberry Pi 5 — Infra02 | Observability and monitoring services |
| TrueNAS SCALE | ZFS storage, applications, media services, and GPU-enabled VM workloads |
| Dell OptiPlex 3050 — Proxmox | Virtualization, security testing, and lab workloads |

Detailed architecture and diagrams are available in [ARCHITECTURE.md](ARCHITECTURE.md).

---

## Network

The network is built around a **UniFi Cloud Gateway Fiber (UCG-Fiber)** with multi-gigabit switching and Wi-Fi 6/7 access points.

### Network Features

- UniFi UCG-Fiber
- 10 GbE backbone
- 2.5 GbE access switching
- UniFi U6 Pro
- UniFi U7 Pro
- VLAN segmentation
- Dedicated IoT network
- Isolated security-lab network
- Split DNS
- Cloudflare DNS
- Caddy reverse proxy
- HTTPS/TLS for externally accessible services

### VLAN Architecture

| VLAN | Purpose |
|---|---|
| VLAN 1 | Default/trusted network |
| VLAN 30 | IoT |
| VLAN 66 | Isolated security lab |

These are the three networks currently deployed. The IoT network is separated
by VLAN but does not currently have network isolation enabled.

---

## Core Infrastructure

### Infra01 — Raspberry Pi 5

Lightweight infrastructure host running Debian and Docker.

Services include:

- Homepage
- Portainer
- Uptime Kuma
- Caddy
- Authentik

### Infra02 — Raspberry Pi 5

NVMe-backed observability and operations host running Debian 13 and Docker.

Services include:

- Prometheus
- Grafana
- PVE Exporter
- Graphite Exporter
- Node Exporter

### TrueNAS SCALE

Primary storage and application platform.

Responsibilities include:

- ZFS storage
- Media storage
- Photo management
- Personal cloud services
- Application hosting
- Virtual machine hosting
- AI/ML workloads

Major services include:

- Immich
- Jellyfin
- Jellystat
- Vaultwarden
- Jellyseerr
- Bazarr
- FlareSolverr
- Gluetun
- Jackett
- Radarr
- Sonarr
- Prowlarr
- qBittorrent
- Recyclarr
- Cloudflare tunnels

### Proxmox

Dell OptiPlex 3050 Micro used as a dedicated virtualization and security lab host.

Workloads include:

- Kali Linux
- Windows security testing / malware analysis VM
- Temporary lab workloads

---

## Media Automation

The media environment uses an automated download and library-management stack:

```text
Prowlarr
   │
   ├─────────────┐
   ▼             ▼
Sonarr         Radarr
   │             │
   └──────┬──────┘
          ▼
     qBittorrent
          │
          ▼
       Storage
          │
          ▼
       Jellyfin
```

### Hardlink Storage Architecture

The media stack uses a unified ZFS dataset so downloads and library files reside on the same filesystem.

```text
media-data/
├── downloads/
└── media/
    ├── Movies/
    └── TV/
```

Containers use a common `/data` mount:

```text
/data/downloads
/data/media/Movies
/data/media/TV
```

This allows Radarr and Sonarr to import completed downloads using **hardlinks instead of creating duplicate physical copies**.

Hardlink functionality has been validated using inode and link-count verification.

---

## AI & Machine Learning

### UbuntuLLM

A dedicated Ubuntu VM hosted on TrueNAS provides local GPU-accelerated AI services.

Hardware acceleration is provided through PCI passthrough of an:

**NVIDIA GeForce GTX 1070 — 8 GB VRAM**

The VM currently provides:

- Ollama
- Open WebUI
- Immich Machine Learning
- NVIDIA CUDA runtime

GPU inference has been verified for both local LLM workloads and Immich machine-learning workloads.

### Local LLMs

Ollama provides local model inference with GPU acceleration.

Models tested include:

- Llama 3.2 3B
- Qwen 3 8B
- Qwen 2.5 VL 3B

A project-specific GPT has also been added as a conversational companion for
the homelab. It is used to preserve lab context, guide controlled exercises,
and help interpret results; the repository remains the authoritative record of
implemented infrastructure and validated findings.

Open WebUI provides the browser interface for interacting with locally hosted models.

---

## Immich GPU Acceleration

Immich's machine-learning workload is separated from the primary Immich server.

```text
┌─────────────────────┐
│       Immich        │
│    TrueNAS SCALE    │
└──────────┬──────────┘
           │
           │ Machine Learning API
           ▼
┌─────────────────────┐
│     UbuntuLLM       │
│                     │
│  Immich ML + CUDA   │
└──────────┬──────────┘
           │
           ▼
     NVIDIA GTX 1070
```

The remote Immich ML container uses the CUDA-enabled Immich image.

CUDA acceleration has been verified through ONNX Runtime using:

```text
CUDAExecutionProvider
CPUExecutionProvider
```

GPU acceleration is currently used for:

- Smart Search
- CLIP image embeddings
- Semantic image search
- Face detection
- Duplicate detection

Initial Smart Search processing successfully indexed more than **6,000 assets** using the GTX 1070.

---

## Identity & Access

Authentik provides the foundation for centralized authentication and SSO across supported services.

Current capabilities:

- Self-hosted identity provider
- Reverse-proxy integration
- HTTPS/TLS
- Central authentication platform

Planned work includes expanding:

- SSO coverage
- MFA
- Application integrations
- Authentication policies

---

## Remote Access

Externally accessible services are protected using HTTPS and exposed through a combination of Cloudflare and reverse-proxy infrastructure.

```text
Internet
   │
   ▼
Cloudflare
   │
   ▼
Reverse Proxy / Tunnel
   │
   ├── Jellyfin
   ├── Immich
   ├── Vaultwarden
   ├── Jellyseerr
   ├── Jellystat
   ├── Grafana (through Authentik)
   └── Authentik
```

Split DNS allows the same service names to resolve appropriately from both internal and external networks.

---

## Security

Security is a primary design consideration rather than an afterthought.

### Implemented

- VLAN network segmentation
- Dedicated IoT VLAN
- Security-Lab isolation
- HTTPS/TLS
- Reverse proxy architecture
- Split DNS
- Cloudflare integration
- Authentik identity provider
- Local password management with Vaultwarden
- Dedicated security-testing VMs
- Isolated VLAN 66 security lab
- Restricted desktop-to-lab RDP management path
- Sysmon endpoint telemetry with the SwiftOnSecurity configuration
- Windows Firewall dropped-packet logging
- Validated red/blue correlation for blocked SYN scans and permitted RDP
  service enumeration

### Security Lab

The Proxmox security lab currently pairs Kali Linux (`10.66.6.122`) with a
Windows sandbox (`winSB`, `10.66.6.115`) on isolated VLAN 66. Inter-VLAN
access is denied except for a tested TCP/3389 management rule from the trusted
desktop (`10.10.10.115`) to `winSB`.

The first purple-team cycle was completed by executing controlled Nmap activity
from Kali and correlating the attacker view with Windows telemetry:

- A TCP SYN scan produced Windows Firewall `DROP` records for blocked RPC,
  NetBIOS, and SMB probes on ports 135, 139, and 445.
- An `nmap -sV -Pn -p 3389` probe identified RDP while Sysmon Event ID 3
  recorded the inbound connections from Kali to `winSB`.
- Sysmon identified `svchost.exe`, `NT AUTHORITY\NETWORK SERVICE`, the `RDP`
  rule, and `Initiated: false`; it recorded the connection, not attribution to
  Nmap itself.
- The Windows sandbox timezone was corrected from Pacific to Eastern, the
  Windows Time service was enabled, and synchronization with
  `time.windows.com` was verified so Kali and Windows evidence can be reliably
  correlated.

### Planned

- Wazuh
- CrowdSec
- Centralized security logging
- Additional SSO/MFA integrations
- Expanded monitoring and alerting

---

## Monitoring

### Current

- Uptime Kuma
- Jellystat
- UniFi monitoring
- TrueNAS monitoring
- Docker/container health monitoring
- Prometheus
- Grafana
- Proxmox and Raspberry Pi host dashboards

### Planned

- Loki
- Centralized infrastructure dashboards

---

## Current Roadmap

### Foundation

- [x] Docker
- [x] Cloudflare
- [x] Caddy
- [x] HTTPS/TLS
- [x] Reverse proxy
- [x] Split DNS
- [x] VLAN segmentation
- [x] Multi-gigabit networking
- [x] 10 GbE infrastructure

### Identity

- [x] Authentik deployed
- [ ] Expand SSO integrations
- [ ] Expand MFA coverage

### Media

- [x] Jellyfin
- [x] Jellystat
- [x] Sonarr
- [x] Radarr
- [x] Prowlarr
- [x] qBittorrent
- [x] Recyclarr
- [x] Hardlink-enabled storage
- [x] Jellyseerr

### AI / ML

- [x] UbuntuLLM VM
- [x] GTX 1070 PCI passthrough
- [x] NVIDIA CUDA
- [x] Ollama
- [x] Open WebUI
- [x] Immich remote machine learning
- [x] Immich CUDA acceleration
- [x] Immich Smart Search

### Monitoring

- [x] Uptime Kuma
- [x] Prometheus
- [x] Grafana
- [ ] Loki

### Security

- [x] Isolated VLAN 66 security lab
- [x] Kali and Windows sandbox baseline
- [x] Sysmon and Windows Firewall telemetry
- [x] Initial purple-team red/blue correlation
- [ ] Wazuh
- [ ] CrowdSec
- [ ] Centralized security logging

### Automation

- [ ] Ansible
- [ ] GitHub Actions
- [ ] Automated configuration backups
- [ ] Infrastructure-as-Code expansion

### Future Services

- [ ] Paperless-ngx
- [ ] Additional Authentik integrations
- [ ] Expanded local AI services

---

## Documentation

Additional technical documentation is maintained throughout the repository.

- [Architecture](ARCHITECTURE.md)
- [Changelog](CHANGELOG.md)
- `docs/` — general and cross-cutting documentation
- `infrastructure/` — physical systems and host roles
- `network/` — networking configuration and documentation
- `docker/` — container configuration
- `diagrams/` — architecture and topology diagrams
- `scripts/` — administration and automation scripts

---

## Project Goals

This homelab is an ongoing engineering environment used to explore and develop practical experience with:

- Network architecture and segmentation
- Cybersecurity engineering
- Linux administration
- Docker and containerization
- Virtualization
- ZFS and storage architecture
- Identity and access management
- Reverse proxies and PKI/TLS
- Infrastructure monitoring
- Infrastructure automation
- Local LLM inference
- GPU passthrough
- Machine-learning infrastructure
- Self-hosted services

The environment evolves continuously as new technologies and architecture patterns are tested and integrated.

---

## Change History

Major infrastructure milestones and migrations are documented in
[CHANGELOG.md](CHANGELOG.md).
