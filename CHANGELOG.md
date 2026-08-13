# Long's Enterprise Homelab

Enterprise-inspired self-hosted infrastructure focused on security,
automation, monitoring, networking, storage, AI/ML, and infrastructure engineering.

---

# Current Infrastructure

## Network

- UniFi Cloud Gateway Fiber (UCG-Fiber)
- UniFi U6 Pro
- UniFi U7 Pro
- 10 GbE / 2.5 GbE switching
- PPPoE Internet connection
- VLAN-segmented network
- Internal DNS / Split DNS
- Cloudflare DNS
- Caddy Reverse Proxy

### Network Segmentation

- LAN — 10.10.10.0/24
- VLAN 10 — Infrastructure
- VLAN 30 — IoT
- VLAN 40 — Guest
- VLAN 50 — Servers

---

# Compute

## Infra01

Raspberry Pi 5 running Debian.

Infrastructure services include:

- Docker
- Portainer
- Homepage
- Uptime Kuma
- Caddy
- Authentik

## Proxmox

Dell OptiPlex 3050 Micro.

Current VMs:

- Kali Linux
- Windows Sandbox / Malware Analysis VM

## TrueNAS SCALE

Primary storage and application server.

Hardware:

- ASUS X99-A
- Intel Core i7-6800K
- NVIDIA GTX 1070
- ZFS storage

Primary workloads:

- Media
- Photos
- Cloud storage
- Password management
- Finance
- Virtual machines
- AI/ML

---

# Current Services

## Infrastructure

- Homepage
- Portainer
- Uptime Kuma
- Caddy
- Authentik
- Cloudflare DNS / Tunnels

## Media

- Jellyfin
- Jellystat
- Radarr
- Sonarr
- Prowlarr
- qBittorrent
- Recyclarr

## Storage / Personal Cloud

- Nextcloud
- Immich
- Vaultwarden
- Firefly III

---

# Media Storage Architecture

The media stack was migrated to a unified ZFS dataset to support
hardlinks between the download client and the *arr applications.

Host dataset:

/mnt/everything/media-data

Container layout:

/data
├── downloads
└── media
    ├── Movies
    └── TV

Radarr:

/data/media/Movies

Sonarr:

/data/media/TV

qBittorrent:

/data/downloads

Using a common `/data` mount allows completed downloads to be imported
using hardlinks instead of creating duplicate copies.

Hardlink operation verified using matching inode numbers between
download and media-library files.

---

# AI / Machine Learning

## UbuntuLLM VM

Ubuntu VM hosted on TrueNAS SCALE.

GPU:

- NVIDIA GeForce GTX 1070
- 8 GB VRAM
- PCI passthrough
- NVIDIA Driver 580.173.02
- CUDA 13.0

VM networking:

- VirtIO NIC
- TrueNAS bridge: br0
- Static IP: 10.10.10.249

The bridge allows direct communication between the TrueNAS host,
applications, and the UbuntuLLM VM.

## Ollama

Local LLM inference provided by Ollama.

Models tested:

- llama3.2:3b
- qwen3:8b
- qwen2.5vl:3b

GPU inference verified on the GTX 1070.

## Open WebUI

Open WebUI provides a browser-based interface for locally hosted
Ollama models.

---

# Immich Machine Learning

Immich machine learning has been moved to the UbuntuLLM VM so the
GTX 1070 can provide CUDA acceleration.

Architecture:

Immich Server (TrueNAS)
        │
        │ TCP 3003
        ▼
UbuntuLLM VM
10.10.10.249
        │
        ▼
Immich Machine Learning
        │
        ▼
NVIDIA GTX 1070

Container:

ghcr.io/immich-app/immich-machine-learning:v3.0.3-cuda

Machine Learning Endpoint:

http://10.10.10.249:3003

CUDA execution verified using ONNX Runtime:

CUDAExecutionProvider
CPUExecutionProvider

GPU utilization confirmed during Smart Search processing.

Smart Search successfully indexed 6,000+ photo/video assets.

Current ML functionality:

- Smart Search
- CLIP image embeddings
- Semantic image search
- Duplicate detection
- Facial recognition

---

# External Access

Internet
│
▼
Cloudflare DNS
│
▼
Caddy / Cloudflare Tunnels
│
├── Homepage
├── Jellyfin
├── Immich
├── Vaultwarden
├── Nextcloud
├── Portainer
├── Uptime Kuma
└── Authentik

TLS certificates are automatically managed.

---

# Security Architecture

Implemented:

- VLAN segmentation
- HTTPS / TLS
- Reverse proxy
- Split DNS
- Cloudflare DNS
- Authentik identity provider
- Dedicated infrastructure network
- Dedicated server network
- Isolated IoT network
- Guest network
- Local password management with Vaultwarden

Planned:

- Wazuh
- CrowdSec
- Centralized logging
- Additional MFA / SSO integrations

---

# Current Roadmap

## Foundation

- [x] Docker
- [x] Cloudflare
- [x] Caddy
- [x] TLS
- [x] Reverse Proxy
- [x] Split DNS
- [x] VLAN segmentation
- [x] 10 GbE infrastructure

## Identity

- [x] Authentik deployed
- [ ] Expand SSO integration
- [ ] Expand MFA integration

## Media Automation

- [x] Jellyfin
- [x] Radarr
- [x] Sonarr
- [x] Prowlarr
- [x] qBittorrent
- [x] Recyclarr
- [x] Hardlink-enabled storage architecture
- [ ] Jellyseerr

## AI / Machine Learning

- [x] UbuntuLLM VM
- [x] GTX 1070 PCI passthrough
- [x] NVIDIA CUDA
- [x] Ollama
- [x] Open WebUI
- [x] Immich CUDA Machine Learning
- [x] Immich Smart Search

## Monitoring

- [x] Uptime Kuma
- [ ] Prometheus
- [ ] Grafana
- [ ] Loki

## Security

- [ ] Wazuh
- [ ] CrowdSec
- [ ] Centralized security logging

## Automation

- [ ] Ansible
- [ ] GitHub Actions
- [ ] Automated configuration backups
- [ ] Automated infrastructure documentation

## Future Services

- [ ] Paperless-ngx
- [ ] Additional Authentik integrations
- [ ] Expanded local AI services

---

# Changelog — 2026-08-13

Major infrastructure changes:

- Migrated media storage to `/mnt/everything/media-data`
- Consolidated downloads and media into a hardlink-compatible dataset
- Updated Radarr and Sonarr library paths
- Verified hardlinks between qBittorrent downloads and media libraries
- Removed legacy media/download ZFS datasets
- Added TrueNAS `br0` network bridge
- Migrated UbuntuLLM VM networking to the bridge
- UbuntuLLM now reachable directly from TrueNAS
- Configured NVIDIA Container Toolkit
- Verified CUDA access from Docker
- Deployed CUDA-enabled Immich Machine Learning container
- Connected TrueNAS Immich server to remote ML service
- Verified CUDA execution with ONNX Runtime
- Completed Smart Search indexing for 6,000+ assets
- Verified GPU-accelerated semantic image search