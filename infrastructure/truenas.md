# TrueNAS SCALE

TrueNAS is the primary ZFS storage, application, and virtual-machine platform.
Its management address is `10.10.10.254`. One physical interface connects
directly to UCG Fiber SFP+ port 6 at 10 GbE through a DAC.

## Storage

| Dataset or pool | Mount point | Purpose |
|---|---|---|
| `protected` | `/mnt/protected` | Application state, photos, VMs, and backups |
| `protected/appdata` | `/mnt/protected/appdata` | Persistent application configuration and databases |
| `protected/photos` | `/mnt/protected/photos` | Immich photo library |
| `protected/VM` | `/mnt/protected/VM` | Virtual-machine storage |
| `staging` | `/mnt/staging-new` | Staging pool root |
| `staging/staging` | `/mnt/staging` | Active staging hierarchy |
| `staging/staging/media-data` | `/mnt/staging/media-data` | Downloads and media libraries |

The former `/mnt/everything` paths no longer exist.

### Media layout

```text
/mnt/staging/media-data/
├── downloads/
│   └── incomplete/
└── media/
    ├── Movies/
    └── TV/
```

qBittorrent, Radarr, Sonarr, and Bazarr mount the dataset at `/data`. Keeping
downloads and imported libraries in one ZFS dataset permits hardlink imports.

## Applications

| Application | Image/version | Host port | Persistent storage |
|---|---|---:|---|
| Jellyfin | `jellyfin/jellyfin:10.11.11` | 30013; 30014 for TLS | protected config; staging cache and media |
| Immich Server | `immich-server:v3.0.3` | 30041 | `/mnt/protected/photos/immich` |
| Immich ML | `immich-machine-learning:v3.0.3` | internal | managed ML cache |
| Immich PostgreSQL | PostgreSQL 18 / VectorChord 0.5.3 | internal | `/mnt/protected/appdata/immich-db` |
| Immich Valkey | `valkey:9.1.1` | internal | managed Redis/Valkey volume |
| Vaultwarden | `vaultwarden/server:1.37.0` | 30032 | `/mnt/protected/appdata/vaultwarden-data` |
| Vaultwarden PostgreSQL | `postgres:18.4-trixie` | internal | `/mnt/protected/appdata/vaultwarden-db` |
| Jellystat | `cyfershepard/jellystat:1.1.11` | 30176 | protected backup directory |
| Jellystat PostgreSQL | `postgres:18.4-trixie` | internal | `/mnt/protected/appdata/jellystat-db` |
| Jackett | `home-operations/jackett:0.24.2265` | 30118 | `/mnt/protected/appdata/jackett` |
| Recyclarr | `recyclarr/recyclarr:8.7.0` | internal | `/mnt/protected/appdata/recyclarr` |

The media stack additionally runs Bazarr, FlareSolverr, Gluetun, Jellyseerr,
Prowlarr, qBittorrent, Radarr, and Sonarr. qBittorrent shares Gluetun's network
namespace; only Gluetun publishes qBittorrent Web UI and torrent ports.

Jellyfin and the native Immich ML container have no directly mapped GPU.
GPU-accelerated Immich inference remains offloaded to the UbuntuLLM VM.

TrueNAS stores its rendered media-stack file beneath
`/mnt/.ix-apps/app_configs/media-stack`. The sanitized repository copy is a
reference representation; TrueNAS remains the deployment authority.

## Cloudflare Routes

| Public hostname | Origin |
|---|---|
| `jellyfin.longn.ca` | `http://10.10.10.254:30013` |
| `imm.longn.ca` | `https://10.10.10.254:30041` |
| `vaultwarden.longn.ca` | `http://10.10.10.254:30032` |
| `flix.longn.ca` | `http://10.10.10.254:5055` |
| `jellystat.longn.ca` | `http://10.10.10.254:30176` |

Tunnel tokens and application credentials are intentionally excluded.
