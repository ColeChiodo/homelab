# Seerr

Media request portal. Allows users to request movies and TV shows, which are then sent to Radarr/Sonarr for automatic downloading.

## Usage

```bash
docker compose up -d
```

Access the web UI at `http://<host-ip>:5055`.

## Volumes

| Host path | Container path | Purpose |
|---|---|---|
| `/srv/seerr/config` | `/app/config` | Configuration and database |

## Permissions

```bash
sudo chown -R 1000:1000 /srv/seerr
```

Seerr runs without an explicit UID/GID. The `/srv/seerr/config` directory must be writable by the container's default user (uid 1000).

## Backup

```bash
sudo tar czf backup-seerr.tar.gz /srv/seerr/config
```

## Restore

1. Extract the archive ensuring `/srv/seerr/config` is restored.
2. Fix ownership: `sudo chown -R 1000:1000 /srv/seerr`
3. Start the container: `docker compose up -d`
