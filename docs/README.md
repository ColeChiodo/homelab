# Docs

## Recreation

Step-by-step guide to recreate the homelab from scratch.

| Page | Description |
|---|---|---|
| [Overview](recreation/index.md) | VM specs and step list |
| [VM 1 — Homelab](recreation/vm-homelab.md) | Ubuntu setup, disks, Cloudflare tunnel, Docker, *arr apps |
| [VM 2 — Clients](recreation/vm-clients.md) | Client VM, cloudflared systemd services, Docker compose |
| [Dev Environment](recreation/dev-environment.md) | Neovim, oh-my-bash, stow, dotfiles, opencode |

## Variables to track

Customize these values to match your environment.

| Variable | Example | Used in | Description |
|---|---|---|---|
| `VM_USERNAME` | `username` | vm-homelab, vm-clients, dev-env | Linux user on both VMs |
| `VM100_ID` | `100` | vm-homelab | Proxmox VM ID for homelab |
| `VM101_ID` | `101` | vm-clients | Proxmox VM ID for clients |
| `DRIVE_BY_ID` | `Seagate_Expansion_NZ0745M3-0:0` | vm-homelab | Stable by-id path for passthrough disk |
| `DRIVE_DEVICE` | `sdb1` | vm-homelab | Device node inside the VM |
| `MOUNT_POINT` | `/srv/nextcloud-data` | vm-homelab | Mount target for forwarded disk |
| `CLOUDFLARE_TUNNEL_NAME` | `homelab` | vm-homelab | Cloudflare tunnel name |
| `CLOUDFLARE_DOMAIN` | `example.com` | vm-homelab | Domain for tunnel DNS routes |
| `JELLYFIN_SUBDOMAIN` | `jellyfin` | vm-homelab | Jellyfin subdomain (→ `jellyfin.example.com`) |
| `SEERR_SUBDOMAIN` | `seerr` | vm-homelab | Seerr subdomain (→ `seerr.example.com`) |
| `CLIENT_NAME` | `client1` | vm-clients | Client identifier for systemd service |
| `BACKUP_PREFIX` | `prowlarr-backup` | vm-homelab | Prefix of your backup archives |
| `MEDIA_DIR` | `/srv/media` | vm-homelab | Media library directory |
