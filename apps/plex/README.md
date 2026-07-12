# Plex

Media server for streaming movies, TV shows, and music to any device.

## Usage

```bash
docker compose up -d
```

Access the web UI at `http://<host-ip>:32400/web`.

## Volumes

| Host path | Container path | Purpose |
|---|---|---|
| `./config` | `/config` | Configuration, metadata, and database |
| `/path/to/shows` | `/tv` | TV show library |
| `/path/to/movies` | `/movies` | Movie library |

## Permissions

```bash
sudo chown -R 1000:1000 apps/plex/config
```

The container runs with `PUID=1000` / `PGID=1000`. Media directories must be readable by this user.

**Note:** If `apps/plex/config` was created by Docker while running as root, you may need to fix ownership:

```bash
sudo chown -R 1000:1000 apps/plex/config
```

## Backup

```bash
tar czf backup-plex.tar.gz apps/plex/config
```

## Restore

1. Extract the archive ensuring `apps/plex/config` is restored.
2. Fix ownership: `sudo chown -R 1000:1000 apps/plex/config`
3. Start the container: `docker compose up -d`
4. Claim your server at `https://plex.tv/claim` if needed.
