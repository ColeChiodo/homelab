# qBittorrent

Torrent client with built-in WireGuard VPN support.

## Usage

```bash
docker compose up -d
```

Access the web UI at `http://<host-ip>:8081`. Default credentials: `admin` / `changeme`.

## Volumes

| Host path | Container path | Purpose |
|---|---|---|
| `./config` | `/config` | Configuration and database |
| `/path/to/downloads` | `/downloads` | Download directory |

## Permissions

```bash
sudo chown -R 1000:1000 apps/qbittorrent/config
```

**Note:** If `apps/qbittorrent/config` was created by Docker while running as root, you may need to fix ownership:

```bash
sudo chown -R 1000:1000 apps/qbittorrent/config
```

The `/downloads` directory must be writable by the container's user (uid 1000).

## Backup

```bash
tar czf backup-qbittorrent.tar.gz apps/qbittorrent/config
```

## Restore

1. Extract the archive ensuring `apps/qbittorrent/config` is restored.
2. Fix ownership: `sudo chown -R 1000:1000 apps/qbittorrent/config`
3. Start the container: `docker compose up -d`
