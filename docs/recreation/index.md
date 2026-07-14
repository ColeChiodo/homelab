# Recreation Overview

Install Proxmox on bare-metal, then create two VMs to run the homelab services.

## VMs

| VM | ID | Role | Specs |
|---|---|---|---|
| **Homelab** | 100 | Media server, cloud storage, *arr apps | 6 core, 16GB RAM, 128GB storage |
| **Clients** | 101 | Client app hosting | 4 core, 4GB RAM, 32GB storage |

## Steps

1. [VM 1 — Homelab](vm-homelab.md) — Ubuntu setup, disks, Cloudflare tunnel, Docker, apps
2. [VM 2 — Clients](vm-clients.md) — Client VM setup, cloudflared services, Docker compose
3. [Dev Environment](dev-environment.md) — nvim, oh-my-bash, stow, dotfiles, opencode
