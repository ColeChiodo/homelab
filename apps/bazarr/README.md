# Bazarr

Subtitle management for Radarr and Sonarr.

## Usage

```bash
docker compose up -d
```

## Volumes

| Host path | Container path | Purpose |
|---|---|---|
| `/srv/bazarr/config` | `/config` | Configuration and database |
| `/path/to/media` | `/media` | Media library root (same as Radarr/Sonarr) |

## Permissions

```bash
sudo chown -R 1000:1000 /srv/bazarr
```

The `PUID=1000` / `PGID=1000` in `compose.yml` must match the ownership of `/srv/bazarr/config`.

## Backup

```bash
sudo tar czf backup-bazarr.tar.gz /srv/bazarr/config
```

## Restore

1. Extract the archive ensuring `/srv/bazarr/config` is restored.
2. Fix ownership: `sudo chown -R 1000:1000 /srv/bazarr`
3. Start the container: `docker compose up -d`

## Notes

- The `config/config.yaml` file contains API keys and credentials that must be configured after deployment. The template has been sanitized — replace all empty values before use.
