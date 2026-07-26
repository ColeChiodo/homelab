# Nextcloud

All-in-one Nextcloud deployment.

## Usage

```bash
docker compose up -d
```

## Services

| Service | Port | Purpose |
|---|---|---|
| Nextcloud AIO | 8085 | Nextcloud master container |

## Volumes

| Host path | Container path | Purpose |
|---|---|---|
| `nextcloud_aio_mastercontainer` | `/mnt/docker-aio-config` | AIO config (Docker volume) |
| `/var/run/docker.sock` | `/var/run/docker.sock` | Docker socket (for container mgmt) |
| `/srv/nextcloud-data` | `/mnt/ncdata` | Nextcloud file storage |

## Permissions

```bash
sudo chown -R 1000:1000 /srv/nextcloud-data
```

The Nextcloud data directory must be writable by the container's user (uid 1000). The AIO master container uses a Docker volume and manages its own permissions.

## Backup

```bash
# Nextcloud files
sudo tar czf backup-nextcloud-data.tar.gz /srv/nextcloud-data

# AIO master container config (Docker volume)
docker run --rm -v nextcloud_aio_mastercontainer:/config alpine tar czf - -C /mnt/docker-aio-config . > backup-nextcloud-aio-config.tar.gz
```

## Restore

1. Extract the Nextcloud data backup into `/srv/nextcloud-data`.
2. Restore the AIO Docker volume:
   ```bash
   docker run --rm -v nextcloud_aio_mastercontainer:/config alpine sh -c 'tar xzf - -C /mnt/docker-aio-config' < backup-nextcloud-aio-config.tar.gz
   ```
3. Fix ownership: `sudo chown -R 1000:1000 /srv/nextcloud-data`
4. Start the containers: `docker compose up -d`
5. Add Nextcloud Apache to the NPM `proxy` docker network: `sudo docker network connect proxy nextcloud-aio-apache`

## Notes

- The `update-nc-dns.sh` script is a template — fill in your Cloudflare API credentials before use.
