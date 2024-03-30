├── data
├── docker-compose.yml
├── etc
│   └── prometheus
│       └── prometheus.yml

# docker compose
```bash
version: '3.8'

services:
  prometheus:
    #definir un user root
    user: 0:0
    image: quay.io/prometheus/prometheus:v2.0.0
    # Remove container_name parameter
    volumes:
     - ./etc/prometheus/:/etc/prometheus/
     - ./data/prometheus/:/prometheus/
    # command d'execution du process de prometheus où on va preciser ou il y a le config file du prometheus à l'interieur du conteneur et sa base de donnée de prometheus
    command: "--config.file=/etc/prometheus/prometheus.yml --storage.tsdb.path=/prometheus"
    ports:
     - 9090:9090

  grafana:
    image: grafana/grafana
    container_name: grafana
    ports:
     - 3333:3000
    volumes:
      - grafana-data:/var/lib/grafana
      - ./etc/grafana/:/etc/grafana/provisioning/
    depends_on:
      - prometheus

volumes:
  grafana-data:
```
# prometheus.yml
```bash
global:
  scrape_interval:     5s # By default, scrape targets every 15 seconds.
  evaluation_interval: 5s # By default, scrape targets every 15 seconds.
  # scrape_timeout is set to the global default (10s).

  # Attach these labels to any time series or alerts when communicating with
  # external systems (federation, remote storage, Alertmanager).
  external_labels:
    monitor: 'codelab-monitor'

# Load rules once and periodically evaluate them according to the global 'evaluation_interval'.
rule_files:
  # - "first.rules"
  # - "second.rules"

# A scrape configuration containing exactly one endpoint to scrape:
# Here it's Prometheus itself.
scrape_configs:
- job_name: 'node'
  static_configs:
  - targets: ['localhost:9100']
```
# test node exporter
bash```
wget https://github.com/prometheus/node_exporter/releases/download/v0.18.1/node_exporter-0.18.1.linux-amd64.tar.gz
sudo tar -xzvf node_exporter-0.18.1.linux-amd64.tar.gz -C /usr/local/bin
sudo /usr/local/bin/node_exporter-0.18.1.linux-amd64/node_exporter
```

