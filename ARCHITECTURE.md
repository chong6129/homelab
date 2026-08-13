# Homelab Architecture

Technical architecture for Long's enterprise-inspired homelab.

The environment is designed around separation of responsibilities across
networking, infrastructure services, storage, virtualization, media,
identity, monitoring, and GPU-accelerated AI workloads.

---

## Architecture Overview

> Insert current architecture diagram here.

The environment consists of three primary compute platforms:

| Platform | Primary Role |
|---|---|
| Raspberry Pi 5 — Infra01 | Core infrastructure and management services |
| TrueNAS SCALE | ZFS storage, applications, media, and GPU-enabled virtualization |
| Dell OptiPlex 3050 — Proxmox | Virtualization and security testing |

Network connectivity and segmentation are provided by the UniFi
infrastructure.

---

# Network Architecture

## Core Network

The network is built around a UniFi Cloud Gateway Fiber (UCG-Fiber).

Infrastructure includes:

- UniFi UCG-Fiber
- 10 GbE backbone connectivity
- 2.5 GbE access switching
- UniFi U6 Pro
- UniFi U7 Pro
- VLAN segmentation
- Split DNS
- Cloudflare DNS

The primary trusted LAN uses:

    10.10.10.0/24

---

## VLAN Segmentation

| VLAN | Purpose |
|---|---|
| LAN | Trusted client devices |
| VLAN 10 | Infrastructure |
| VLAN 30 | IoT |
| VLAN 40 | Guest |
| VLAN 50 | Servers |

Segmentation separates infrastructure, servers, untrusted IoT devices,
guest devices, and trusted clients.

Inter-VLAN communication is controlled at the gateway.

---

# Compute Architecture

## Infra01

**Platform:** Raspberry Pi 5  
**Operating System:** Debian

Infra01 hosts lightweight infrastructure services that should remain
independent from the primary storage server.

Current services include:

- Docker
- Portainer
- Homepage
- Uptime Kuma
- Caddy
- Authentik

This allows core management and access services to remain available
independently of TrueNAS application workloads.

---

## TrueNAS SCALE

TrueNAS is the primary storage and application platform.

### Responsibilities

- ZFS storage
- Media storage
- Photo library
- Personal cloud storage
- Application hosting
- Virtual machines
- Media automation
- Backup/snapshot infrastructure

### Major Applications

- Immich
- Jellyfin
- Jellystat
- Nextcloud
- Vaultwarden
- Firefly III
- Sonarr
- Radarr
- Prowlarr
- qBittorrent
- Recyclarr

---

## Proxmox

**Platform:** Dell OptiPlex 3050 Micro

Proxmox provides an independent virtualization environment primarily used
for testing and security workloads.

Current workloads include:

- Kali Linux
- Windows security testing / malware analysis VM
- Temporary lab systems

Keeping these workloads separate from TrueNAS reduces dependency on the
primary storage/application host.

---

# TrueNAS Storage Architecture

ZFS provides the underlying storage architecture.

Storage is organized by workload rather than exposing unrelated
applications to the same paths.

---

## Media Dataset

The automated media stack uses a unified dataset:

    /mnt/everything/media-data

Layout:

    media-data/
    ├── downloads/
    └── media/
        ├── Movies/
        └── TV/

The unified dataset is intentional.

Previously, downloads and media libraries existed in separate ZFS
datasets. Because ZFS datasets are separate filesystems, Radarr and
Sonarr could not create hardlinks between downloaded files and library
files.

The media stack was therefore migrated into a common dataset.

---

## Container Storage Mapping

Sonarr, Radarr, and qBittorrent share the same container-side `/data`
filesystem hierarchy.

    Host
    /mnt/everything/media-data
                │
                ▼
    Container
    /data
    ├── downloads
    └── media
        ├── Movies
        └── TV

Application paths:

| Application | Path |
|---|---|
| qBittorrent | `/data/downloads` |
| Radarr | `/data/media/Movies` |
| Sonarr | `/data/media/TV` |

This prevents Docker path translation issues and allows hardlinks to be
created between completed downloads and imported library files.

---

## Hardlink Workflow

Example movie workflow:

    Prowlarr
        │
        ▼
      Radarr
        │
        ▼
    qBittorrent
        │
        ▼
    /data/downloads
        │
        │ hardlink
        ▼
    /data/media/Movies
        │
        ▼
     Jellyfin

TV follows the same architecture through Sonarr.

Hardlink operation was validated by comparing inode numbers and filesystem
link counts between files in the download and media directories.

Example:

    Download file
        inode 7125
        links=2

    Library file
        inode 7125
        links=2

Both paths therefore reference the same underlying ZFS file data.

---

# Media Automation Architecture

The media automation stack consists of:

    ┌─────────────┐
    │  Prowlarr   │
    └──────┬──────┘
           │
       ┌───┴────┐
       ▼        ▼
    Sonarr    Radarr
       │        │
       └───┬────┘
           ▼
      qBittorrent
           │
           ▼
       Downloads
           │
      Hardlink Import
           │
       ┌───┴────┐
       ▼        ▼
       TV      Movies
       │        │
       └───┬────┘
           ▼
        Jellyfin

Recyclarr is used to maintain media quality profiles and related
configuration.

---

# TrueNAS Virtual Networking

TrueNAS uses a Linux bridge for communication between the host, physical
network, and virtual machines.

## Bridge

Bridge interface:

    br0

Physical interface:

    ens4f1np1

Architecture:

    Physical LAN
         │
         ▼
    ens4f1np1
         │
         ▼
        br0
       ┌─┴─────────────┐
       │               │
       ▼               ▼
    TrueNAS         Virtual Machines

The TrueNAS management address resides on `br0` rather than directly on
the physical interface.

This architecture allows TrueNAS-hosted applications to communicate
directly with virtual machines.

---

## Why the Bridge Was Required

The UbuntuLLM VM was initially attached directly to the physical
TrueNAS interface.

This allowed the VM to communicate with other LAN devices but prevented
direct communication between the TrueNAS host and the VM.

Symptoms included:

    TrueNAS → UbuntuLLM
    Destination Host Unreachable

while other LAN devices could access UbuntuLLM normally.

Moving the physical interface and VM onto `br0` resolved the
host-to-guest communication limitation.

---

# AI / ML Architecture

## UbuntuLLM

UbuntuLLM is a dedicated Ubuntu virtual machine hosted by TrueNAS.

Its primary purpose is providing GPU-accelerated local AI services.

### GPU

    NVIDIA GeForce GTX 1070
    VRAM: 8 GB
    PCI Passthrough: Enabled

The GPU is passed directly from TrueNAS to UbuntuLLM.

NVIDIA drivers and CUDA are installed inside the guest rather than on the
TrueNAS host.

---

## GPU Architecture

    TrueNAS
       │
       │ PCI Passthrough
       ▼
    UbuntuLLM
       │
       ▼
    NVIDIA GTX 1070
       │
       ├── Ollama
       │
       └── Immich Machine Learning

This allows multiple local AI services to make use of the dedicated GPU.

---

# Ollama

Ollama provides local LLM inference.

Service endpoint:

    TCP/11434

Models tested include:

- Llama 3.2 3B
- Qwen 3 8B
- Qwen 2.5 VL 3B

GPU inference has been verified using NVIDIA monitoring.

Open WebUI provides a browser-based frontend for Ollama.

---

# Immich Machine Learning

The primary Immich application runs on TrueNAS, while machine-learning
inference is offloaded to UbuntuLLM.

Architecture:

    ┌────────────────────────┐
    │         Immich         │
    │      TrueNAS SCALE     │
    └───────────┬────────────┘
                │
                │ TCP/3003
                ▼
    ┌────────────────────────┐
    │       UbuntuLLM        │
    │                        │
    │ Immich Machine Learning│
    │         CUDA           │
    └───────────┬────────────┘
                │
                ▼
          NVIDIA GTX 1070

The Immich ML container uses the CUDA-enabled Immich machine-learning
image.

---

## CUDA Runtime

Docker GPU access is provided using NVIDIA Container Toolkit.

GPU container access was validated using an NVIDIA CUDA container and
`nvidia-smi`.

Immich Machine Learning uses ONNX Runtime.

Execution provider preference:

    CUDAExecutionProvider
    CPUExecutionProvider

This allows CUDA execution with CPU fallback where required.

---

## Immich ML Workloads

GPU acceleration is used for workloads including:

- CLIP image embeddings
- Smart Search
- Semantic image search
- Face detection
- Duplicate detection

Initial Smart Search indexing processed more than 6,000 existing assets.

GPU activity was verified through:

- GPU utilization
- Increased GPU power consumption
- VRAM allocation
- ONNX Runtime CUDA provider logs

---

# Identity Architecture

Authentik provides the foundation for centralized authentication.

    User
      │
      ▼
    Caddy
      │
      ▼
    Authentik
      │
      ▼
    Protected Service

Current work focuses on expanding Authentik integration across supported
applications.

Future identity improvements include:

- Additional SSO integrations
- MFA expansion
- Authentication policies
- Application-specific access controls

---

# External Access Architecture

Externally accessible applications use Cloudflare and reverse-proxy
infrastructure.

    Internet
       │
       ▼
    Cloudflare
       │
       ▼
    Caddy / Cloudflare Tunnel
       │
       ├── Homepage
       ├── Jellyfin
       ├── Immich
       ├── Vaultwarden
       ├── Nextcloud
       ├── Portainer
       ├── Uptime Kuma
       └── Authentik

HTTPS/TLS is used for externally accessible services.

Split DNS allows internal clients to reach services using the same
hostnames without unnecessarily traversing external infrastructure.

---

# Security Architecture

Security controls currently implemented include:

- VLAN segmentation
- Trusted/untrusted network separation
- Dedicated infrastructure VLAN
- Dedicated server VLAN
- IoT isolation
- Guest isolation
- HTTPS/TLS
- Reverse proxy
- Split DNS
- Centralized identity foundation
- Local password management
- Dedicated security testing environment

Planned security capabilities include:

- Wazuh
- CrowdSec
- Centralized security logging
- Expanded MFA
- Additional monitoring and alerting

---

# Monitoring Architecture

Current monitoring includes:

- Uptime Kuma
- Jellystat
- UniFi monitoring
- TrueNAS monitoring
- Docker health checks

Future observability stack:

    Infrastructure
         │
         ▼
     Prometheus
         │
         ▼
       Grafana

Logs:

    Infrastructure
         │
         ▼
        Loki
         │
         ▼
       Grafana

Security telemetry may later be integrated with Wazuh.

---

# Design Principles

The homelab follows several general architecture principles.

## Separation of Responsibilities

Storage, infrastructure, virtualization, security testing, and GPU
workloads are separated where practical.

## Network Segmentation

Systems are grouped according to function and trust level rather than
placing all devices on a flat network.

## Internal-First Services

Services remain locally hosted wherever practical, with external access
added selectively.

## Secure External Access

Externally exposed applications use TLS, reverse-proxy controls, and
centralized DNS.

## Shared Storage Where Appropriate

Applications requiring filesystem-level operations such as hardlinks use
a deliberately shared storage hierarchy.

## Hardware Reuse

Existing hardware is repurposed for dedicated infrastructure roles,
including the GTX 1070 for local AI and machine-learning acceleration.

## Incremental Architecture

New services are introduced in stages and integrated into existing
networking, identity, storage, monitoring, and security controls rather
than deployed as isolated applications.

---

# Related Documentation

- [README](README.md) — project overview and current capabilities
- [CHANGELOG](CHANGELOG.md) — major infrastructure changes and milestones
- `docs/` — implementation documentation
- `network/` — network configuration and documentation
- `docker/` — container configuration
- `diagrams/` — architecture diagrams
- `Scripts/` — administration and automation