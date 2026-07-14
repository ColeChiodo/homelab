# VM 2 — Clients

Separate VM for hosting client apps.

## Create VM

Create VM using Ubuntu Server ISO. ID 101.
- 4 core, 4GB RAM, 32GB storage
- Install OpenSSH server
- After install, shutdown
- Delete ISO from hardware
- Enable QEMU Guest Agent
- Log into VM, install `qemu-guest-agent`

Set standard UFW rules, install Cloudflare and Docker (see [VM 1 — Homelab](vm-homelab.md) for details).

Pull the GitHub repo for clients and run `docker compose up`.

## Cloudflare Systemd Services

Create a systemd service for each client tunnel. Example `/etc/systemd/system/cloudflared-client1.service`:

```ini
[Unit]
Description=Cloudflare Tunnel - Client 1
After=network-online.target
Wants=network-online.target

[Service]
Type=notify
ExecStart=/usr/local/bin/cloudflared --config /home/<user>/.cloudflared/client1.yml tunnel run
Restart=on-failure
RestartSec=5

[Install]
WantedBy=multi-user.target
```

Start the service:

```bash
sudo systemctl daemon-reload
sudo systemctl enable cloudflared-client1
sudo systemctl start cloudflared-client1
```
