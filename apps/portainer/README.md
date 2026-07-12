# Portainer

Container management web UI for Docker.

## Usage

```bash
docker compose up -d
```

Access the web UI at `https://localhost:9000`.

## Volumes

| Host path | Container path | Purpose |
|---|---|---|
| `/var/run/docker.sock` | `/var/run/docker.sock` | Docker socket |
| `/srv/portainer/data` | `/data` | Persistent data |

## Permissions

```bash
sudo chown -R 1000:1000 /srv/portainer
```

Portainer runs as root inside the container (needed for Docker socket access), but the data directory should be accessible.

## Backup

```bash
sudo tar czf backup-portainer.tar.gz /srv/portainer/data
```

## Restore

1. Extract the archive ensuring `/srv/portainer/data` is restored.
2. Fix ownership: `sudo chown -R 1000:1000 /srv/portainer`
3. Start the container: `docker compose up -d`
4. Re-create your admin account at first login.
