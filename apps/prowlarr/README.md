# Prowlarr

Indexer and torrent-tracker aggregator for the \*arr ecosystem. Provides a unified indexer API to Radarr, Sonarr, Lidarr, etc.

## Deployment

This instance runs as a **systemd service**, not Docker. See `systemd/prowlarr.service`.

## Data

| Path | Purpose |
|---|---|
| `/var/lib/prowlarr/` | Configuration and database |
| `config/Definitions/` | Indexer definition templates (510+ YAML files) |

## Permissions

```bash
sudo chown -R prowlarr:prowlarr /var/lib/prowlarr
```

The service runs as `User=prowlarr` / `Group=prowlarr`.

## Backup

```bash
sudo tar czf backup-prowlarr.tar.gz /var/lib/prowlarr
```

## Restore

1. Ensure `/var/lib/prowlarr` exists.
2. Extract the archive into `/var/lib/prowlarr`.
3. Fix ownership: `sudo chown -R prowlarr:prowlarr /var/lib/prowlarr`
4. Start the service: `sudo systemctl start prowlarr`

## Notes

- Indexer definitions in `config/Definitions/` are Cardigann templates containing only placeholder variables (`{{ .Config.apikey }}`). Actual credentials are configured through the Prowlarr web UI.
