# Homelab

Media server, cloud storage, monitoring, and self-hosted services on Proxmox.

## Layout

```
apps/          Docker Compose files and systemd units, one subdirectory per service
docs/          Full recreation guides and reference documentation
system/        Host-level configuration (storage, networking, backup)
opentofu/      (placeholder) Infrastructure-as-code
ansible/       (placeholder) Configuration management
```

## Services

| Service | Type | Port | Data path | Method |
|---|---|---|---|---|
| Bazarr | Subtitle management | 6767 | `/srv/bazarr/config` | Docker |
| FlareSolverr | Cloudflare bypass proxy | 8191 | `/srv/flaresolverr` | Docker |
| Jellyfin | Media server | 8096 | `/srv/jellyfin` | Docker |
| Kavita | Manga/comic reader | 5000 | `/srv/kavita/config` | Docker |
| Nextcloud AIO | Cloud storage | 8085 | `/srv/nextcloud-data` + Docker volume | Docker |
| Nginx Proxy Manager | Reverse proxy | 80, 81, 443 | `/srv/npm` | Docker |
| Plex | Media server | host | `apps/plex/config` | Docker |
| Portainer | Docker management UI | 9000 | `/srv/portainer/data` | Docker |
| Prowlarr | Indexer aggregator | 9696 | `/var/lib/prowlarr` | systemd |
| qBittorrent | Torrent client + VPN | 8081 | `/srv/qbittorrent/config` | Docker |
| Radarr | Movie management | 7878 | `/var/lib/radarr` | systemd |
| Seerr | Media request portal | 5055 | `/srv/seerr/config` | Docker |
| Sonarr | TV series management | 8989 | `/var/lib/sonarr` | systemd |
| Tailscale | Mesh VPN | — | host-level | Native |
| Vaultwarden | Password manager | 8087 | `/srv/vaultwarden` | Docker |

### Monitoring stack

| Service | Port | Data path |
|---|---|---|
| Prometheus | 9090 | `/srv/prometheus` |
| Grafana | 9091 | `/srv/grafana` |
| Proxmox VE Exporter | 9221 | — |
| cAdvisor | 9080 | — |
| Alertmanager | 9093 | — |
| Blackbox Exporter | 9115 | — |

Run via `apps/monitoring/compose.yml`.

## Storage

| Mount | Purpose |
|---|---|
| `/srv/<app>/` | Application data (config, state) |
| `/srv/media/` | Media library (movies, shows, manga, comics, books) |

See `system/storage/` for details.

## Permissions

Docker apps run as `PUID=1000 PGID=1000` (or `user: "1000:1000"`):

```bash
sudo chown -R 1000:1000 /srv/bazarr /srv/jellyfin /srv/kavita /srv/grafana \
  /srv/prometheus /srv/npm /srv/portainer /srv/seerr /srv/qbittorrent /srv/flaresolverr
```

systemd services run as dedicated system users:

```bash
sudo chown -R radarr:media     /var/lib/radarr
sudo chown -R sonarr:media     /var/lib/sonarr
sudo chown -R prowlarr:prowlarr /var/lib/prowlarr
```

Media library directories must be readable by uid 1000 (Docker apps) and the `media` group (systemd apps).

## Networking

- **Reverse proxy**: Nginx Proxy Manager (port 81 for admin UI) routes HTTP/HTTPS traffic via the shared Docker `proxy` network.
- **Mesh VPN**: Tailscale provides secure remote access to services.
- **Firewall**: UFW rules managed via `system/networking/firewall/`.

## Backup

| Service | Path to back up | Note |
|---|---|---|
| Bazarr | `/srv/bazarr/config` | |
| FlareSolverr | `/srv/flaresolverr` | |
| Jellyfin | `/srv/jellyfin/config` | Exclude `/srv/jellyfin/cache` |
| Kavita | `/srv/kavita/config` | |
| Nextcloud | `/srv/nextcloud-data` + `nextcloud_aio_mastercontainer` volume | |
| NPM | `/srv/npm/data` | Exclude `letsencrypt/` (auto-renewed) |
| Plex | `apps/plex/config` | |
| Portainer | `/srv/portainer/data` | |
| Prowlarr | `/var/lib/prowlarr` | |
| qBittorrent | `/srv/qbittorrent/config` | |
| Radarr | `/var/lib/radarr` | |
| Seerr | `/srv/seerr/config` | |
| Sonarr | `/var/lib/sonarr` | |
| Vaultwarden | `/srv/vaultwarden` | |

```bash
# Docker configs
sudo tar czf backup-docker.tar.gz \
  /srv/bazarr/config /srv/flaresolverr /srv/jellyfin/config \
  /srv/kavita/config /srv/npm/data /srv/portainer/data \
  /srv/seerr/config /srv/qbittorrent/config /srv/vaultwarden \
  apps/plex/config

# systemd configs
sudo tar czf backup-systemd.tar.gz \
  /var/lib/radarr /var/lib/sonarr /var/lib/prowlarr

# Nextcloud AIO config volume
docker run --rm -v nextcloud_aio_mastercontainer:/config alpine \
  tar czf - -C /mnt/docker-aio-config . > backup-nc-aio.tar.gz
```

See `docs/` for full recreation steps.
