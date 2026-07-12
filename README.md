# Homelab

Media server and cloud infrastructure running on bare-metal Ubuntu.

## Services

| App | Type | Port | Data path | Install |
|---|---|---|---|---|
| **Bazarr** | Subtitle management | 6767 | `/srv/bazarr/config` | Docker |
| **FlareSolverr** | Cloudflare bypass proxy | 8191 | `/srv/flaresolverr` | Docker |
| **Jellyfin** | Media server | host | `/srv/jellyfin` | Docker |
| **Kavita** | Manga/comic reader | 5000 | `./config` (local) | Docker |
| **Nextcloud** | Cloud storage + AIO | 8085 | `/srv/nextcloud-data` | Docker |
| **Plex** | Media server | host | `./config` (local) | Docker |
| **Portainer** | Docker management UI | 9000 | `/srv/portainer/data` | Docker |
| **Prowlarr** | Indexer aggregator | — | `/var/lib/prowlarr` | systemd |
| **qBittorrent** | Torrent client + VPN | 8081 | `./config` (local) | Docker |
| **Radarr** | Movie management | — | `/var/lib/radarr` | systemd |
| **Seerr** | Media request portal | 5055 | `/srv/seerr/config` | Docker |
| **Sonarr** | TV series management | — | `/var/lib/sonarr` | systemd |

## Permissions

Most Docker services run with `PUID=1000 PGID=1000` (or `user: "1000:1000"`). Host data directories must be owned by this user:

```bash
sudo chown -R 1000:1000 /srv/bazarr /srv/jellyfin /srv/portainer /srv/seerr /srv/flaresolverr
```

systemd services (Radarr, Sonarr, Prowlarr) run as their own system users:

```bash
# Radarr: user=radarr, group=media
# Sonarr: user=sonarr,  group=media
# Prowlarr: user=prowlarr, group=prowlarr
sudo chown -R radarr:media   /var/lib/radarr
sudo chown -R sonarr:media   /var/lib/sonarr
sudo chown -R prowlarr:prowlarr /var/lib/prowlarr
```

Local `./config` directories (Kavita, Plex, qBittorrent, Nextcloud NPM) should be owned by `1000:1000` or the user running Docker.

Media library directories must be readable by uid 1000 (for Docker apps) and by the `media` group (for systemd apps).

## Backup

### What to back up

| App | Backup path | Exclude |
|---|---|---|
| Bazarr | `/srv/bazarr/config` | — |
| FlareSolverr | `/srv/flaresolverr` | — |
| Jellyfin | `/srv/jellyfin/config` | `/srv/jellyfin/cache` |
| Kavita | `apps/kavita/config/` | — |
| Nextcloud | `apps/nextcloud/npm/`, AIO Docker volume, `/srv/nextcloud-data` | `npm/letsencrypt` (auto-renewed) |
| Plex | `apps/plex/config/` | — |
| Portainer | `/srv/portainer/data` | — |
| Prowlarr | `/var/lib/prowlarr/` | — |
| qBittorrent | `apps/qbittorrent/config/` | — |
| Radarr | `/var/lib/radarr/` | — |
| Seerr | `/srv/seerr/config` | — |
| Sonarr | `/var/lib/sonarr/` | — |

### Quick backup

```bash
# Docker app configs
sudo tar czf backup-docker-configs.tar.gz \
  /srv/bazarr/config \
  /srv/flaresolverr \
  /srv/jellyfin/config \
  /srv/portainer/data \
  /srv/seerr/config \
  apps/kavita/config \
  apps/plex/config \
  apps/qbittorrent/config

# systemd app configs
sudo tar czf backup-systemd-configs.tar.gz \
  /var/lib/radarr \
  /var/lib/sonarr \
  /var/lib/prowlarr

# Nextcloud
sudo tar czf backup-nextcloud.tar.gz \
  apps/nextcloud/npm/data \
  /srv/nextcloud-data
docker run --rm -v nextcloud_aio_mastercontainer:/config alpine tar czf - -C /mnt/docker-aio-config . > backup-nextcloud-aio-config.tar.gz
```

## Restore

1. Ensure the target directories exist and have correct ownership (see **Permissions** above).
2. Extract the backup archive into the appropriate paths.
3. Start the service:
   - **Docker**: `docker compose up -d` (from `apps/<app>/`)
   - **systemd**: `sudo systemctl start <app>`
