# Long's Enterprise Homelab

Enterprise-inspired self-hosted infrastructure built for hands-on experience with **networking, cybersecurity, virtualization, storage, automation, observability, and local AI/ML**.

The lab is designed as more than a collection of self-hosted applications. It provides an environment for experimenting with infrastructure architecture, network segmentation, identity, secure remote access, GPU workloads, storage design, and operational monitoring.

---

## Architecture

The environment is distributed across three primary compute platforms:

| Platform | Role |
|---|---|
| Raspberry Pi 5 — Infra01 | Core infrastructure and management services |
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
- Dedicated infrastructure network
- Dedicated server network
- Isolated IoT network
- Guest network
- Split DNS
- Cloudflare DNS
- Caddy reverse proxy
- HTTPS/TLS for externally accessible services

### VLAN Architecture

| VLAN | Purpose |
|---|---|
| LAN | Trusted client devices |
| VLAN 10 | Infrastructure |
| VLAN 30 | IoT |
| VLAN 40 | Guest |
| VLAN 50 | Servers |

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
- Nextcloud
- Vaultwarden
- Firefly III
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
   ├── Homepage
   ├── Jellyfin
   ├── Immich
   ├── Vaultwarden
   ├── Nextcloud
   ├── Portainer
   ├── Uptime Kuma
   └── Authentik
```

Split DNS allows the same service names to resolve appropriately from both internal and external networks.

---

## Security

Security is a primary design consideration rather than an afterthought.

### Implemented

- VLAN network segmentation
- Dedicated infrastructure network
- Dedicated server network
- IoT isolation
- Guest network isolation
- HTTPS/TLS
- Reverse proxy architecture
- Split DNS
- Cloudflare integration
- Authentik identity provider
- Local password management with Vaultwarden
- Dedicated security-testing VMs

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

### Planned

- Prometheus
- Grafana
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
- [ ] Jellyseerr

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
- [ ] Prometheus
- [ ] Grafana
- [ ] Loki

### Security

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
- `docs/` — service and implementation documentation
- `network/` — networking configuration and documentation
- `docker/` — container configuration
- `diagrams/` — architecture and topology diagrams
- `Scripts/` — administration and automation scripts

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