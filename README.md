# Prometheus + Grafana + Node Exporter — Docker Compose

Infrastructure monitoring stack deployed with Docker Compose on Ubuntu (WSL2). Collects host-level metrics using Node Exporter, stores them in Prometheus, and visualizes them in Grafana.

---

## Stack

| Service | Image | Port | Role |
|---|---|---|---|
| Prometheus | `prom/prometheus:latest` | 9090 | Metrics storage & scraping |
| Node Exporter | `prom/node-exporter:latest` | 9100 | Host metrics collector |
| Grafana | `grafana/grafana:latest` | 3000 | Visualization & dashboards |

---

## Architecture

```
Host (CPU / RAM / Disk / Network)
          ↓
   Node Exporter :9100      ← exposes host metrics
          ↓
    Prometheus :9090         ← scrapes & stores metrics every 15s
          ↓
     Grafana :3000           ← visualizes metrics in dashboards
```

---

## Prerequisites

- Docker Desktop with WSL2 integration enabled
- Ubuntu (WSL2 distro)
- Docker Compose plugin

---

## Quick Start

```bash
# Clone the repository
git clone https://github.com/gonzalocedoce/prometheus-grafana-docker.git
cd prometheus-grafana-docker

# Start the stack
docker compose up -d

# Verify all three containers are running
docker compose ps
```

---

## Access

| Service | URL | Credentials |
|---|---|---|
| Grafana | http://localhost:3000 | admin / admin123 |
| Prometheus | http://localhost:9090 | — |
| Node Exporter | http://localhost:9100/metrics | — |

---

## Project Structure

```
prometheus-grafana-docker/
├── docker-compose.yml          # Defines all three services
├── prometheus/
│   └── prometheus.yml          # Scrape config for Prometheus
├── screenshots/
│   ├── dashboard.png           # Grafana Node Exporter Full dashboard
│   ├── prometheus-targets.png  # Prometheus targets showing UP status
│   └── node-exporter-metrics.png # Raw metrics exposed by Node Exporter
└── README.md
```

---

## Configuration

### prometheus/prometheus.yml

Defines the scrape interval and targets:

```yaml
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'prometheus'
    static_configs:
      - targets: ['localhost:9090']

  - job_name: 'node-exporter'
    static_configs:
      - targets: ['node-exporter:9100']
```

Node Exporter is referenced by its Docker Compose service name — DNS resolution is handled internally by Docker.

---

## Dashboard

Uses the official **Node Exporter Full** dashboard (ID: 1860) from Grafana Labs.

Metrics visualized:
- CPU usage per core and total
- Memory usage (used, free, cache, swap)
- Disk space and I/O operations
- Network traffic (inbound/outbound)
- System uptime and load average

![Grafana Dashboard](screenshots/dashboard.png)

---

## Prometheus Targets

Both targets show **UP** status, confirming Prometheus is successfully scraping metrics.

![Prometheus Targets](screenshots/prometheus-targets.png)

---

## Node Exporter Metrics

Raw metrics exposed at `http://localhost:9100/metrics` in Prometheus exposition format.

![Node Exporter Metrics](screenshots/node-exporter-metrics.png)

---

## Useful Commands

```bash
# Stop the stack
docker compose down

# Stop and remove volumes (resets all data)
docker compose down -v

# View logs
docker compose logs -f

# Restart a single service
docker compose restart grafana
```

---

## Notes

- Deployed on **Ubuntu 24.04 via WSL2** on Windows
- Data is persisted using named Docker volumes (`prometheus_data`, `grafana_data`)
- Prometheus retention is set to **15 days**
- Some host metrics (CPU, RAM) may show limited data when running inside WSL2 due to container isolation from the Windows host

---

## Author

**Hector Gonzalo Correa Jimenez**  
Cloud Engineer | IAM & Cloud Security  
[LinkedIn](https://linkedin.com/in/hgonzalo-correa) · [GitHub](https://github.com/gonzalocedoce)
