## Install Prometheus and Grafana

### Install node_exporter

The node_exporter is a small application that exports server metrics on port 9100 on any server installed on. (or 9182 on Microsoft Windows servers). This will be installed on all the servers you want to monitor

Install that with the following steps:

````bash
sudo apt -y install node_exporter
````

### Prometheus Server

Prometheus is part of the Ubuntu apt repositories. Keep in mind it is not the newest version of the upstream project. To install it use the following commands:

```bash
sudo apt -y install prometheus
```

This will install prometheus which will listen on your localhost at `http://localhost:9090` by default it will start scraping your local host. This won't be on much use until you configure targets to scrape.

This is done by editing the `/etc/prometheus/prometheus.yml` file.

```yaml
# Sample config for Prometheus.

global:
  scrape_interval:     15s # Set the scrape interval to every 15 seconds. Default is every 1 minute.
  evaluation_interval: 15s # Evaluate rules every 15 seconds. The default is every 1 minute.
  # scrape_timeout is set to the global default (10s).

  # Attach these labels to any time series or alerts when communicating with
  # external systems (federation, remote storage, Alertmanager).
  external_labels:
      monitor: 'example-monitor-staging'

# Alertmanager configuration
alerting:
  alertmanagers:
  - static_configs:
    - targets: ['localhost:9093']

# Load rules once and periodically evaluate them according to the global 'evaluation_interval'.
rule_files:
  # - "first_rules.yml"
  # - "second_rules.yml"

# A scrape configuration containing exactly one endpoint to scrape:
# Here it's Prometheus itself.
scrape_configs:
  # The job name is added as a label `job=<job_name>` to any timeseries scraped from this config.
  - job_name: 'node'

    # Override the global default and scrape targets from this job every 5 seconds.
    scrape_interval: 5s
    scrape_timeout: 5s

    # metrics_path defaults to '/metrics'
    # scheme defaults to 'http'.

    static_configs:
      - targets: ['localhost:9090']

  - job_name: node
    # If prometheus-node-exporter is installed, grab stats about the local
    # machine by default.
    static_configs:
      - targets: ['0.0.0.0:9090', 'server1:9100', 'server2:9100', 'server3:9100']
  # Above is where you can add new servers to monitor
 
```

### Install grafana

You will need the following steps:

````bash
sudo apt-get install -y apt-transport-https software-properties-common wget
sudo mkdir -p /etc/apt/keyrings/
wget -q -O - https://apt.grafana.com/gpg.key | gpg --dearmor | sudo tee /etc/apt/keyrings/grafana.gpg > /dev/null
wget -q -O - https://apt.grafana.com/gpg.key | gpg --dearmor | sudo tee /etc/apt/keyrings/grafana.gpg > /dev/null
echo "deb [signed-by=/etc/apt/keyrings/grafana.gpg] https://apt.grafana.com stable main" | sudo tee -a /etc/apt/sources.list.d/grafana.list
sudo apt -y update
# Installs the latest OSS release:
sudo apt-get install grafana
sudo /bin/systemctl daemon-reload
sudo /bin/systemctl enable grafana-server
````

Grafana will listen on port 3000 by default. Will need to install a webserver that will proxy to this port with the following steps:

````bash
sudo apt -y install nginx
````
Configure the webserver with the following:

````conf
# this is required to proxy Grafana Live WebSocket connections.
map $http_upgrade $connection_upgrade {
  default upgrade;
  '' close;
}

upstream grafana {
  server localhost:3000;
}

server {
  listen 80;
  root /usr/share/nginx/html;
  index index.html index.htm;

  location / {
    proxy_set_header Host $http_host;
    proxy_pass http://grafana;
  }

# Proxy Grafana Live WebSocket connections.
  location /api/live/ {
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection $connection_upgrade;
    proxy_set_header Host $http_host;
    proxy_pass http://grafana;
  }
}
````
The Grafana project has documentation on how to enable [Google OAuth2](https://grafana.com/docs/grafana/latest/setup-grafana/configure-security/configure-authentication/google/)

