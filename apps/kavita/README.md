# Kavita

Manga, comic, and book reader with a web-based UI.

## Usage

```bash
docker compose up -d
```

## Volumes

| Host path | Container path | Purpose |
|---|---|---|
| `./config` | `/kavita/config` | Configuration and database |
| `/path/to/manga` | `/manga` | Manga library |
| `/path/to/comics` | `/comics` | Comic library |
| `/path/to/books` | `/books` | Book library |

## Permissions

```bash
sudo chown -R 1000:1000 apps/kavita/config
```

Kavita runs without an explicit UID/GID. The `./config` directory on the host must be writable by the container's default user (which runs as uid 1000 or the host user running Docker).

## Backup

```bash
tar czf backup-kavita.tar.gz apps/kavita/config
```

## Restore

1. Extract the archive ensuring `apps/kavita/config` is restored.
2. Fix ownership: `sudo chown -R 1000:1000 apps/kavita/config`
3. Start the container: `docker compose up -d`

## Notes

- The `config/appsettings.json` file contains a `TokenKey` for JWT signing. The template has been sanitized — Kavita will regenerate a new key on first startup if it's empty.
