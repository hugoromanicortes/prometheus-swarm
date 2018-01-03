# Prometheus
Prometheus is a monitoring framework

## Getting Started
This project configure a Prometheus Server, an Alert Manager, Grafana and the Node Exporter.

## Components
### Prometheus [https://prometheus.io/]
Prometheus is an open-source systems monitoring and alerting toolkit originally built at SoundCloud. Since its inception in 2012, many companies and organizations have adopted Prometheus, and the project has a very active developer and user community. It is now a standalone open source project and maintained independently of any company. To emphasize this, and to clarify the project's governance structure, Prometheus joined the Cloud Native Computing Foundation in 2016 as the second hosted project, after Kubernetes.

### Grafana [https://grafana.com/]
Grafana is an open source metric analytics & visualization suite. It is most commonly used for visualizing time series data for infrastructure and application analytics but many use it in other domains including industrial sensors, home automation, weather, and process control.

### Alertmanager [https://prometheus.io/docs/alerting/alertmanager/]
The Alertmanager handles alerts sent by client applications such as the Prometheus server. It takes care of deduplicating, grouping, and routing them to the correct receiver integration such as email, PagerDuty, or OpsGenie. It also takes care of silencing and inhibition of alerts.

### Node Exporter [https://github.com/prometheus/node_exporter]
Prometheus exporter for hardware and OS metrics exposed by *NIX kernels, written in Go with pluggable metric collectors.


## Configuration
### Prometheus

### Grafana

### Alertmanager

## Run the framework

```
docker-compose up -d
```