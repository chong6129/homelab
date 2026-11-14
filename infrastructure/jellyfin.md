# Jellyfin Experience and Media Operations

Status reconciled 2026-11-13 from the November 10 customization work and
October 28 media-stack diagnostics. TrueNAS remains the deployment authority.

## Home screen customization

The customization work recorded a pre-change Jellyfin backup named
`jellyfin-backup-20261110203356.zip` under Dashboard -> Backups and preserved
the original theme settings outside this repository.

The following changes were implemented and visually verified in Jellyfin Web:

- Cinema-style theme and home layout with Continue Watching and Next Up first.
- Viewing-history suggestions such as “Because You Watched” rows.
- Auto Collections genre rows for Comedy, Romance, and Post-apocalyptic.
- Separate C-Drama and K-Drama collections/rows.
- Trending, Top 10 Movies, and Top 10 TV Shows using IMDb charts matched to
  playable titles in the existing library; ten posters were verified in each
  Top 10 row at that time.

The recorded plugin work installed Home Screen Sections, Plugin Pages, and
Collection Sections, then restarted Jellyfin. SmartLists was subsequently used
for ranking collections. The layout was enabled by default with individual
user overrides retained. Counts and chart membership are point-in-time results,
not fixed library totals.

The verified client was **Jellyfin Web**. Official Android TV collection
browsing and custom web home rows are different client experiences. Moonfin
was suggested for selectable collection rows on TV, but setup on the user's
device was not verified. Windows Desktop rendering was also not directly
verified. Client instructions and plugin versions should be rechecked against
the installed versions before future changes.

## Rotten Tomatoes / Seerr follow-up

The latest request changed the ranking goal to weekly published Rotten
Tomatoes “Most Popular Right Now” movie and TV charts. The user authorized
requests for both charts, including all seasons of missing series, through
LongFlix at `flix.longn.ca`.

The work record reports that LongFlix accepted 21 new movie requests and
requests for 23 series. Accepted requests do not establish completed downloads.
The record then stops while partially available series and separate Rotten
Tomatoes lists were being handled. It does **not** confirm a finished weekly
refresh job, a durable recurring request job, or replacement of the IMDb rows.
Keep these automation tasks open until their saved schedules and successful
runs are verified.

## VPN dependency and startup diagnosis

The October 28 operator-supplied Compose configuration places qBittorrent in
Gluetun's network namespace and waits for `service_healthy`. VPN settings are
supplied through `/mnt/protected/appdata/gluetun/gluetun.env`, outside Git.
The [sanitized media-stack reference](../docker/compose/truenas-media-stack.yml)
has been reconciled with those two confirmed configuration details.

Repeated TrueNAS app-update failures were traced to Gluetun being marked
unhealthy during startup. A later inspection showed Gluetun healthy with no
consecutive failures. Its inherited health check used a five-second interval,
five-second timeout, ten-second startup grace period, and three retries.

Longer startup tolerance was proposed (10-second interval, 60-second startup
grace, six retries), but no returned result confirms that override was applied.
Startup timing remains a hypothesis; the available successful-container logs
do not rule out authentication or connectivity failures in earlier containers.

## Follow-up

Verify the weekly chart/request automation, TV client layout, current server
and plugin inventory, and Gluetun behavior on a controlled future update.
No theme exports, session cookies, API keys, or raw viewing history are committed.
