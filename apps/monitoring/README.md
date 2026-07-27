# Monitoring — Grafana / Prometheus

Infrastructure monitoring stack with Prometheus, Grafana, Alertmanager, cAdvisor, and Blackbox exporter.

## Deployment

```bash
docker compose up -d
```

## Services

| Service | Port | Purpose |
|---|---|---|
| `prometheus` | 9090 | Metrics collection and storage (90d retention) |
| `grafana` | 9091 | Visualization and dashboards |
| `alertmanager` | 9093 | Alert routing and notification |
| `pve-exporter` | 9221 | Proxmox VE metrics exporter |
| `cadvisor` | 9080 | Container resource usage metrics |
| `blackbox-exporter` | 9115 | External endpoint probing (HTTP/HTTPS) |

## Volumes

| Host path | Container path | Purpose |
|---|---|---|
| `/srv/prometheus` | `/prometheus` | TSDB data |
| `/srv/grafana` | `/var/lib/grafana` | Dashboards, config, plugins |

## Permissions

```bash
sudo mkdir -p /srv/prometheus/data /srv/grafana
sudo chown -R 472:472 /srv/grafana   # Grafana runs as UID 472
```

## Node Exporter

Install `prometheus-node-exporter` on each target host:

```bash
sudo apt install -y prometheus-node-exporter
sudo systemctl enable --now prometheus-node-exporter
```

Configure UFW to allow Prometheus access:

```bash
sudo ufw allow from PROMETHEUS_HOST_IP to any port 9100 proto tcp
sudo ufw reload
```

## Proxmox VE Exporter

Create a limited API user on the Proxmox host:

```bash
pveum user add prometheus@pve
pveum role add PrometheusExporter -privs "VM.Audit,Sys.Audit,Datastore.Audit"
pveum acl modify / -user prometheus@pve -role PrometheusExporter
pveum user token add prometheus@pve exporter --privsep 0
```

In `pve-exporter/pve.yml`, replace `YOUR_TOKEN_VALUE` and the Proxmox host IP.

## Alertmanager

In `alertmanager/alertmanager.yml`, replace `YOUR_DISCORD_WEBHOOK_HERE` with a Discord webhook URL.

## Blackbox Exporter

Add URLs to the `blackbox` job in `prometheus/prometheus.yml`. Currently configured for `http_2xx` module.

## Backup

```bash
sudo tar czf backup-prometheus.tar.gz /srv/prometheus
sudo tar czf backup-grafana.tar.gz /srv/grafana
```

## Restore

1. Extract archives into `/srv/prometheus` and `/srv/grafana`.
2. Fix ownership: `sudo chown -R 472:472 /srv/grafana`
3. Start the stack: `docker compose up -d`