# Portainer

Container management web UI for Docker.

## Usage

```
docker compose up -d
```

Access the web UI at `https://localhost:9000`.

## Volumes

| Host path | Container path | Purpose |
|---|---|---|
| `/var/run/docker.sock` | `/var/run/docker.sock` | Docker socket |
| `/srv/portainer/data` | `/data` | Persistent data |

## Permissions

```
sudo chown -R 1000:1000 /srv/portainer
```

Portainer runs as root inside the container (needed for Docker socket access), but the data directory should be accessible.

## Portainer Agent
Since I run 2 VMs in Proxmox, each with docker, I will install portainer on 1 vm, and portainer agent on another to connect the 2 in a single UI.
```
sudo docker volume create portainer_agent_data
```
then
```
sudo docker run -d \
  --name portainer_agent \
  --restart=always \
  -p 9001:9001 \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v portainer_agent_data:/data \
  portainer/agent:latest
```
Make sure your firewall allows: `VM1 IP → VM2 IP : 9001`
On VM2: `sudo ufw allow from <VM1 Local IP> to any port 9001 proto tcp`
Finally, create new environment, agent, set name and location, save.

## Backup

```
sudo tar czf backup-portainer.tar.gz /srv/portainer/data
```

## Restore

1. Extract the archive ensuring `/srv/portainer/data` is restored.
2. Fix ownership: `sudo chown -R 1000:1000 /srv/portainer`
3. Start the container: `docker compose up -d`
4. Re-create your admin account at first login.
