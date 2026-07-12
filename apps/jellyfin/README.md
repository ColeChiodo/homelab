# Jellyfin

Open-source media server. Streams movies, TV shows, music, and photos to any device.

## Usage

```bash
docker compose up -d
```

## Volumes

| Host path | Container path | Purpose |
|---|---|---|
| `/srv/jellyfin/config` | `/config` | Configuration, metadata, and runtime data |
| `/srv/jellyfin/cache` | `/cache` | Transcodes and cache (can be recreated) |
| `/path/to/movies` | `/media/movies` | Movie library |
| `/path/to/shows` | `/media/shows` | TV show library |

## Permissions

```bash
sudo chown -R 1000:1000 /srv/jellyfin
```

The container runs with `user: "1000:1000"` in `compose.yml`. Media library directories must also be readable by this user.

## Backup

```bash
sudo tar czf backup-jellyfin.tar.gz /srv/jellyfin/config
```

Do **not** back up `/srv/jellyfin/cache` — it is disposable and will be recreated.

## Restore

1. Ensure `/srv/jellyfin/config` and `/srv/jellyfin/cache` exist.
2. Extract the config backup into `/srv/jellyfin/config`.
3. Create an empty cache directory: `mkdir -p /srv/jellyfin/cache`
4. Fix ownership: `sudo chown -R 1000:1000 /srv/jellyfin`
5. Update host paths in `compose.yml` to match your environment.
6. Start the container: `docker compose up -d`
