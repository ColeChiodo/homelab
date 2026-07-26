# NGINX Proxy Manager (NPM)

Reverse proxy with a web-based admin UI. Manages proxy hosts, SSL certificates, and access lists.

## Usage

```bash
docker compose up -d
```

Access the admin UI at `http://<host-ip>:81`.

## Prerequisites

A Docker network named `proxy` must exist for other services to connect through:

```bash
sudo docker network create proxy
```

## Volumes

| Host path | Container path | Purpose |
|---|---|---|
| `/srv/npm/data` | `/data` | Configuration and database |
| `/srv/npm/letsencrypt` | `/etc/letsencrypt` | SSL certificates |

## Permissions

```bash
sudo chown -R 1000:1000 /srv/npm
```

The container runs with uid 1000 by default. The data and letsencrypt directories must be writable by this user.

## Backup

```bash
sudo tar czf backup-npm.tar.gz /srv/npm/data
```

Do **not** back up `/srv/npm/letsencrypt` — SSL certificates are auto-renewed by Let's Encrypt.

## Restore

1. Extract the data backup into `/srv/npm/data`.
2. Fix ownership: `sudo chown -R 1000:1000 /srv/npm`
3. Update host paths in `compose.yml` to match your environment.
4. Start the container: `docker compose up -d`
