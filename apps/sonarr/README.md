# Sonarr

TV series collection manager. Automates downloading, organizing, and upgrading TV show libraries.

## Deployment

This instance runs as a **systemd service**, not Docker. See `systemd/sonarr.service`.

## Data

| Path | Purpose |
|---|---|
| `/var/lib/sonarr/` | Configuration and database |
| `config/` | Config XML templates |

## Permissions

```bash
sudo chown -R sonarr:media /var/lib/sonarr
```

The service runs as `User=sonarr` / `Group=media`. Media library directories must be readable by the `media` group.

## Backup

```bash
sudo tar czf backup-sonarr.tar.gz /var/lib/sonarr
```

## Restore

1. Ensure `/var/lib/sonarr` exists.
2. Extract the archive into `/var/lib/sonarr`.
3. Fix ownership: `sudo chown -R sonarr:media /var/lib/sonarr`
4. Start the service: `sudo systemctl start sonarr`
