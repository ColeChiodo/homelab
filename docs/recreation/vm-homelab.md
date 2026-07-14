# VM 1 — Homelab

## Create VM

Create VM using Ubuntu Server ISO. ID 100.
- 6 core, 16GB RAM, 128GB storage
- Install OpenSSH server
- After install, shutdown
- Delete ISO from hardware
- Enable QEMU Guest Agent
- Log into VM, install `qemu-guest-agent`

### UFW

```bash
sudo ufw allow 22/tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw enable
sudo ufw status verbose
```

## Disks

In Proxmox, find drives with `lsblk`, then get the stable ID:

```bash
ls -l /dev/disk/by-id/
```

Use the stable path instead of `/dev/sdX`:

```bash
qm set 100 -scsi1 /dev/disk/by-id/Seagate_Expansion_NZ0745M3-0:0
```

### Back in VM

Mount the forwarded disk:

```bash
sudo mount /dev/sdb1 /srv/nextcloud-data
```

## Install Apps

> Port-forward `80` and `443` in router settings.

### Cloudflare Tunnel

Install cloudflared:

```bash
curl -fsSL https://pkg.cloudflare.com/cloudflare-main.gpg | sudo gpg --dearmor -o /usr/share/keyrings/cloudflare-main.gpg
echo "deb [signed-by=/usr/share/keyrings/cloudflare-main.gpg] https://pkg.cloudflare.com/cloudflared noble main" | sudo tee /etc/apt/sources.list.d/cloudflared.list
sudo apt update
sudo apt install cloudflared
```

Create tunnel:

```bash
cloudflared tunnel login
cloudflared tunnel create homelab
```

Edit `~/.cloudflared/config.yml`.

Create DNS routes:

```bash
cloudflared tunnel route dns homelab jellyfin.example.com
cloudflared tunnel route dns homelab seerr.example.com
```

Install as system service:

```bash
sudo cloudflared service install
sudo systemctl enable cloudflared
sudo systemctl start cloudflared
```

### Docker

Set up Docker's `apt` repository:

```bash
sudo apt update
sudo apt install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

sudo tee /etc/apt/sources.list.d/docker.sources <<EOF
Types: deb
URIs: https://download.docker.com/linux/ubuntu
Suites: $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}")
Components: stable
Architectures: $(dpkg --print-architecture)
Signed-By: /etc/apt/keyrings/docker.asc
EOF

sudo apt update
```

Install Docker packages:

```bash
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

Clone the homelab repo:

```bash
git clone https://github.com/ColeChiodo/homelab.git
mkdir ~/docker
```

### For each app

```bash
cp ~/homelab/apps/nextcloud ~/docker/ -r
cd ~/docker/nextcloud
```

Follow reinstall steps for each app.

> If you are **Cole**, manually secure-copy config files to VM.

### Non-Docker Apps — Sonarr, Radarr, Prowlarr

Use the [*Arr Community Install Script](https://wiki.servarr.com/install-script) for Prowlarr and Radarr:

```bash
curl -o servarr-install-script.sh https://raw.githubusercontent.com/Servarr/Wiki/master/servarr/servarr-install-script.sh
sudo bash servarr-install-script.sh
```

Sonarr installer:

```bash
curl -o install-sonarr.sh https://raw.githubusercontent.com/Sonarr/Sonarr/develop/distribution/debian/install.sh
sudo bash install-sonarr.sh
```

Cleanup:

```bash
sudo rm servarr-install-script.sh
sudo rm install-sonarr.sh
sudo rm Prowlarr.master.*.linux-core-x64.tar.gz
sudo rm Radarr.master.*.linux-core-x64.tar.gz
sudo rm Sonarr.main.*.linux-x64.tar.gz
```

Set UFW rules for local access:

```bash
sudo ufw allow 8989/tcp
sudo ufw allow 7878/tcp
sudo ufw allow 9696/tcp
```

Extract backups to `/srv/`:

```bash
sudo mkdir -p /srv/prowlarr
sudo tar -xzf prowlarr-backup.tar.gz --strip-components=3 -C /srv/prowlarr
sudo mkdir -p /srv/radarr
sudo tar -xzf radarr-backup.tar.gz --strip-components=3 -C /srv/radarr
sudo mkdir -p /srv/sonarr
sudo tar -xzf sonarr-backup.tar.gz --strip-components=3 -C /srv/sonarr
```

Set permissions:

```bash
sudo chown -R prowlarr:media /srv/prowlarr
sudo chown -R radarr:media /srv/radarr
sudo chown -R sonarr:media /srv/sonarr
```

Symlink data directories:

```bash
sudo systemctl stop prowlarr radarr sonarr

sudo mv /var/lib/prowlarr /var/lib/prowlarr.old
sudo ln -s /srv/prowlarr /var/lib/prowlarr

sudo mv /var/lib/radarr /var/lib/radarr.old
sudo ln -s /srv/radarr /var/lib/radarr

sudo mv /var/lib/sonarr /var/lib/sonarr.old
sudo ln -s /srv/sonarr /var/lib/sonarr

sudo systemctl start prowlarr radarr sonarr
```

Ensure media directory is writable by the `media` group:

```bash
sudo chown -R root:media /srv/media
sudo chmod -R 775 /srv/media
```

If everything looks good, delete the backups:

```bash
sudo rm prowlarr-backup.tar.gz radarr-backup.tar.gz sonarr-backup.tar.gz
```
