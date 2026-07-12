# Radarr

Movie collection manager. Automates downloading, organizing, and upgrading movie libraries.

## Deployment

This instance runs as a **systemd service**, not Docker. See `systemd/radarr.service`.

## Data

| Path | Purpose |
|---|---|
| `/var/lib/radarr/` | Configuration and database |
| `config/` | Config XML templates |

## Permissions

```bash
sudo chown -R radarr:media /var/lib/radarr
```

The service runs as `User=radarr` / `Group=media`. Media library directories must be readable by the `media` group.

## Backup

```bash
sudo tar czf backup-radarr.tar.gz /var/lib/radarr
```

## Restore

1. Ensure `/var/lib/radarr` exists.
2. Extract the archive into `/var/lib/radarr`.
3. Fix ownership: `sudo chown -R radarr:media /var/lib/radarr`
4. Start the service: `sudo systemctl start radarr`
