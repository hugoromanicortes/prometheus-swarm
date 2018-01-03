# Prometheus
Prometheus is a monitoring framework

## Getting Started
This project configure a Prometheus Server, an Alert Manager, Grafana and the Node Exporter.

## Components
### Prometheus
Prometheus is an open-source systems monitoring and alerting toolkit originally built at SoundCloud. Since its inception in 2012, many companies and organizations have adopted Prometheus, and the project has a very active developer and user community. It is now a standalone open source project and maintained independently of any company. To emphasize this, and to clarify the project's governance structure, Prometheus joined the Cloud Native Computing Foundation in 2016 as the second hosted project, after Kubernetes.

Website: https://prometheus.io/

### Alertmanager
The Alertmanager handles alerts sent by client applications such as the Prometheus server. It takes care of deduplicating, grouping, and routing them to the correct receiver integration such as email, PagerDuty, or OpsGenie. It also takes care of silencing and inhibition of alerts.

Website: https://prometheus.io/docs/alerting/alertmanager/

### Grafana
Grafana is an open source metric analytics & visualization suite. It is most commonly used for visualizing time series data for infrastructure and application analytics but many use it in other domains including industrial sensors, home automation, weather, and process control.

Website: https://grafana.com/

### Node Exporter
Prometheus exporter for hardware and OS metrics exposed by *NIX kernels, written in Go with pluggable metric collectors.

Website: https://github.com/prometheus/node_exporter


## Configuration
For a complete documentation on configuration please refer to official website. Please note that we are using here a docker-compose.yml file in order to load and configure docker images.

### Prometheus
The main configuration of prometheus is prometheus.yml. This file contains default scraping configuration plus target configuration (i.e. job_name).
In addition we configure rules that will used in order to trigger the alertmanager

```
global:
  scrape_interval: 15s
  evaluation_interval: 15s
  external_labels:
    monitor: my-project
rule_files:
- alert.rules
alerting:
  alertmanagers:
  - scheme: http
    static_configs:
    - targets:
      - alertmanager:9093
scrape_configs:
- job_name: prometheus
  scrape_interval: 5s
  static_configs:
  - targets:
    - localhost:9090
- job_name: node-exporter
  scrape_interval: 5s
  dns_sd_configs:
  - names:
    - tasks.node-exporter
    type: A
    port: 9100
```

### Alertmanager
Alertmanager configuration is also based on external file configuration (config.yml). This file describes what action to trigger in case of notification from Prometheus (e.g generate a message in HipChat)

```
global:
  hipchat_auth_token: xxx
  hipchat_api_url: https://api.hipchat.com/
route:
  group_by:
  - cluster
  receiver: team-hipchat
  routes:
  - match:
      severity: hipchat
    receiver: team-hipchat
receivers:
- name: team-hipchat
  hipchat_configs:
  - auth_token: yyy
    room_id: 12345
    message_format: html
    notify: true
```

### Grafana
Grafana configuration can be very complex. Into this example we simply configure the admin password as follow:

```
GF_SECURITY_ADMIN_PASSWORD=admin
GF_USERS_ALLOW_SIGN_UP=false
```

## Run the framework

```
docker-compose up -d
```