# sobu-productivity-pi-dashboard

A Docker Compose stack for a Raspberry Pi 5 that runs Pi-hole for network-wide DNS filtering and pairs it with Prometheus + Grafana to chart Pi-hole activity.

## Components
- **Pi-hole** – DNS sinkhole running on the host network to serve LAN clients.
- **Pi-hole Exporter** – Prometheus exporter that polls Pi-hole's API for metrics.
- **Prometheus** – Scrapes the exporter and stores time-series data.
- **Grafana** – Pre-provisioned with a Prometheus data source and a Pi-hole overview dashboard.

## Prerequisites
- Raspberry Pi 5 with Docker and Docker Compose installed.
- A static IP assigned to the Pi (used by Pi-hole and the exporter).
- Ports **53 (UDP/TCP)**, **80**, **3000**, and **9090** free on the host.
- A copy of this repository on the Pi.

## Quick start
1. Copy the example environment file and adjust it for your network:
   ```bash
   cp .env.example .env
   # Edit .env to set TZ, WEBPASSWORD, PIHOLE_IPV4, PIHOLE_HOST, and optional ports.
   ```
2. Bring the stack up (Pi-hole needs host networking for DNS):
   ```bash
   docker compose up -d
   ```
3. Point your router or clients at the Pi's IP (`PIHOLE_IPV4`) for DNS.
4. Visit the interfaces:
   - Pi-hole Admin: http://<PIHOLE_IPV4>/admin (password from `WEBPASSWORD`)
   - Prometheus: http://<PIHOLE_IPV4>:9090
   - Grafana: http://<PIHOLE_IPV4>:3000 (defaults: admin/admin unless changed)

## File layout
- `docker-compose.yml` – Service definitions for Pi-hole, exporter, Prometheus, and Grafana.
- `.env.example` – Sample environment variables to configure the stack.
- `config/prometheus/prometheus.yml` – Scrape config targeting the Pi-hole exporter.
- `config/grafana/provisioning/` – Preloaded Prometheus data source and Pi-hole dashboard.
- `data/` – Persistent volumes (created automatically on first run).

## Notes
- The Pi-hole service runs with `network_mode: host` so it can bind to DNS ports without extra iptables rules.
- `pihole-exporter` reaches the Pi-hole API via the host IP set in `PIHOLE_HOST` and exposes metrics to Prometheus on the internal `monitoring` network.
- If Grafana or Prometheus ports conflict with other services, override `GRAFANA_PORT` and `PROMETHEUS_PORT` in `.env` before starting the stack.
