# Homelab Changelog

Major infrastructure changes, deployments, migrations, and milestones.

This file tracks meaningful changes to the homelab rather than routine
maintenance, package updates, or minor configuration changes.

---

## 2026-09-08 — Security Lab Purple-Team Baseline

### Segmentation and Management

- Established the isolated Security Lab on VLAN 66.
- Confirmed Kali at `10.66.6.122` and `winSB` at `10.66.6.115`.
- Validated that VLAN 66 cannot initiate connections to the trusted LAN.
- Added and tested a narrow TCP/3389 management exception from the trusted
  desktop (`10.10.10.115`) to `winSB`.
- Verified RDP and clipboard functionality through the management path.

### Endpoint and Firewall Telemetry

- Verified Sysmon 15.21 running automatically on `winSB` with the
  SwiftOnSecurity configuration.
- Enabled Windows Firewall dropped-packet logging.
- Correlated a controlled Nmap SYN scan with firewall `DROP` records for TCP
  ports 135, 139, and 445.
- Correlated `nmap -sV -Pn -p 3389` service enumeration with inbound Sysmon
  Event ID 3 records from Kali to the RDP service on `winSB`.
- Confirmed that the Sysmon records identify the connection direction,
  endpoints, port, service process, and rule, but do not by themselves identify
  Nmap as the source application.

### Time Synchronization

- Corrected the `winSB` timezone from Pacific to Eastern.
- Enabled the Windows Time service and configured automatic startup.
- Verified synchronization with `time.windows.com` and aligned Kali/Windows
  timestamps for reliable cross-source correlation.

### GPT

- Added a project-specific GPT as a conversational lab companion for guided
  exercises, continuity, and result interpretation.
- Retained this repository as the authoritative source for validated lab state.

---

## 2026-08-13 — Immich GPU Acceleration & Storage Migration

### Immich Machine Learning

- Deployed a dedicated CUDA-enabled Immich Machine Learning container on
  the UbuntuLLM VM.
- Configured Immich to use the remote UbuntuLLM machine-learning endpoint.
- Verified access to the Immich ML and Ollama APIs across the TrueNAS bridge.
- Installed and configured NVIDIA Container Toolkit for Docker.
- Verified Docker access to the NVIDIA GTX 1070.
- Verified Immich ML is using ONNX Runtime with:
  - `CUDAExecutionProvider`
  - `CPUExecutionProvider` fallback
- Verified GPU utilization and VRAM allocation during ML processing.
- Completed Smart Search indexing for 6,000+ existing assets.
- Enabled GPU-accelerated CLIP image embeddings and semantic search.
- Ran duplicate detection against the indexed library.
- Prepared the library for face detection and facial recognition processing.

### UbuntuLLM Networking

- Identified host-to-VM communication failure caused by the VM being
  attached directly to the TrueNAS physical interface.
- Created TrueNAS bridge interface `br0`.
- Migrated the TrueNAS management address to `br0`.
- Attached the UbuntuLLM VirtIO NIC to the new bridge.
- Preserved the VM MAC address and existing static IP configuration.
- Verified direct TrueNAS-to-UbuntuLLM connectivity.
- Verified access to:
  - Immich ML on TCP/3003
  - Ollama on TCP/11434

### Media Storage Migration

- Completed migration of the media stack to the unified ZFS dataset:
  `/mnt/everything/media-data`
- Consolidated downloads and media libraries under a common filesystem
  to support hardlinks.

Current layout:

    /mnt/everything/media-data/
    ├── downloads/
    └── media/
        ├── Movies/
        └── TV/

- Updated qBittorrent to use `/data/downloads`.
- Updated Radarr movie root to `/data/media/Movies`.
- Updated Sonarr TV root to `/data/media/TV`.
- Corrected legacy Radarr `/movies` paths.
- Corrected legacy Sonarr `/tv` paths.
- Updated existing movie and series records to the new root folders.
- Verified completed downloads import successfully into Radarr and Sonarr.
- Verified Jellyfin detects newly imported media.
- Verified hardlinks using matching inode numbers and link counts.
- Confirmed downloaded media and library media no longer require duplicate
  physical copies.
- Migrated remaining legacy TV content into the unified dataset.
- Verified migration integrity with `rsync` dry runs.
- Removed obsolete media/download datasets after migration validation.

### Security / Cleanup

- Identified unexpected `.exe` files masquerading as television releases
  during Sonarr processing.
- Removed the executable files.
- Confirmed legitimate media imports were unaffected.

---

## 2026-08-10 — UbuntuLLM GPU Acceleration

### NVIDIA GPU Passthrough

- Restored UbuntuLLM VM connectivity.
- Verified NVIDIA GTX 1070 PCI passthrough.
- Verified NVIDIA driver `580.173.02`.
- Verified CUDA 13.0 support.
- Confirmed `/dev/nvidia*` devices are available inside Ubuntu.

### Ollama

- Reinstalled and updated Ollama.
- Configured Ollama to listen on:
  `0.0.0.0:11434`
- Verified Ollama API connectivity.
- Confirmed local LLM inference is using the GTX 1070.
- Tested:
  - `llama3.2:3b`
  - `qwen3:8b`
  - `qwen2.5vl:3b`

---

## 2026-08-10 — Media Stack Hardlink Migration

### Storage

- Began migration from separate media/download datasets to a unified
  `media-data` dataset.
- Migrated approximately 1.3 TB of movie data using `rsync -aHAX`.
- Validated the movie migration using an `rsync --delete` dry run.
- Began migration of TV library data.
- Standardized permissions for media directories.

### Media Automation

- Prepared Sonarr, Radarr, Prowlarr, qBittorrent, and Recyclarr for the
  unified `/data` container path.
- Designed the new storage layout specifically to allow hardlink imports.

---

## 2026-07-30 — Reverse Proxy & Service Foundation

### Infrastructure

- Docker application platform operational.
- Cloudflare DNS configured.
- Caddy reverse proxy deployed.
- HTTPS/TLS enabled for externally accessible services.
- Split DNS configured for internal/external service access.

### Services

External and internal access established for:

- Homepage
- Jellyfin
- Immich
- Vaultwarden
- Nextcloud
- Portainer
- Uptime Kuma

### Identity

- Authentik deployed.
- `auth.longn.ca` configured.
- TLS certificate successfully issued.
- Initial SSO infrastructure established.

### Roadmap

Next major phases identified:

1. Expand Authentik SSO integration
2. Complete Jellyseerr / *arr media automation
3. Add Immich AI/ML acceleration
4. Deploy Grafana / Prometheus
5. Deploy Wazuh
6. Deploy CrowdSec
7. Deploy Paperless-ngx
8. Expand infrastructure automation

---

## Earlier Foundation

Initial homelab infrastructure established with:

- UniFi UCG-Fiber
- VLAN-segmented network
- Raspberry Pi 5 infrastructure host
- TrueNAS SCALE storage/application server
- Proxmox virtualization host
- Docker
- Cloudflare
- Caddy
- Jellyfin
- Immich
- Nextcloud
- Vaultwarden
- Homepage
- Portainer
- Uptime Kuma
