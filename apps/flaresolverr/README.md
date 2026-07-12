# FlareSolverr

Proxy server for bypassing Cloudflare anti-bot protection. Used by Prowlarr (and other \*arr apps) to access indexers behind Cloudflare.

## Usage

```bash
docker compose up -d
```

## Volumes

| Host path | Container path | Purpose |
|---|---|---|
| `/srv/flaresolverr` | `/config` | Configuration |

## Permissions

```bash
sudo chown -R 1000:1000 /srv/flaresolverr
```

FlareSolverr does not run with an explicit UID/GID and defaults to root inside the container, but the host directory should still be accessible.

## Backup

```bash
sudo tar czf backup-flaresolverr.tar.gz /srv/flaresolverr
```

## Restore

1. Extract the archive ensuring `/srv/flaresolverr` is restored.
2. Fix ownership: `sudo chown -R 1000:1000 /srv/flaresolverr`
3. Start the container: `docker compose up -d`

## Environment Variables

Refer to the [FlareSolverr documentation](https://github.com/FlareSolverr/FlareSolverr) for available options.
