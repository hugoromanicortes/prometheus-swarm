# Prometheus
Prometheus is a monitoring framework for a Docker infrastructure. It's part of the Cloud Native Computing Foundation (https://www.cncf.io/).

## Getting Started
This project configure a Prometheus Server, an Alert Manager, Grafana and a Node Exporter. These components can deployed in a Docker Swarn cluster (docker-compose) and will come soon with a Kubernetes installation.

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
In addition we configure rules (alert.rules) that will be used in order to trigger the alertmanager

```
# my global config
global:
  scrape_interval:     15s # By default, scrape targets every 15 seconds.
  evaluation_interval: 15s # By default, scrape targets every 15 seconds.
  # scrape_timeout is set to the global default (10s).

  # Attach these labels to any time series or alerts when communicating with
  # external systems (federation, remote storage, Alertmanager).
  external_labels:
      monitor: 'my-project'

# Load and evaluate rules in this file every 'evaluation_interval' seconds.
rule_files:
  - 'alert.rules'
  # - "first.rules"
  # - "second.rules"

# alert
alerting:
  alertmanagers:
  - scheme: http
    static_configs:
    - targets:
      - "alertmanager:9093"

# A scrape configuration containing exactly one endpoint to scrape:
# Here it's Prometheus itself.
scrape_configs:
  # The job name is added as a label `job=<job_name>` to any timeseries scraped from this config.

  - job_name: 'prometheus'

    # Override the global default and scrape targets from this job every 5 seconds.
    scrape_interval: 5s

    static_configs:
         - targets: ['localhost:9090']


  - job_name: 'cadvisor'

    # Override the global default and scrape targets from this job every 5 seconds.
    scrape_interval: 5s

    dns_sd_configs:
    - names:
      - 'tasks.cadvisor'
      type: 'A'
      port: 8080

    # static_configs:
    #      - targets: ['cadvisor:8080']

  - job_name: 'node-exporter'

    # Override the global default and scrape targets from this job every 5 seconds.
    scrape_interval: 5s

    dns_sd_configs:
    - names:
      - 'tasks.node-exporter'
      type: 'A'
      port: 9100
    
    # static_configs:
#      - targets: ['node-exporter:9100']
```

### Alertmanager
Alertmanager configuration is also based on external file configuration (config.yml). This file describes which action to trigger in case of notification from Prometheus (e.g generate a message to HipChat, Slack or other)

```
route:
    receiver: 'slack'

receivers:
    - name: 'slack'
      slack_configs:
          - send_resolved: true
            username: '<username>'
            channel: '#<channel-name>'
            api_url: '<incomming-webhook-url>'
```

### Grafana
Grafana configuration can be very complex. Into this example we simply configure the admin password as follow (components/grafana/config.monitoring):

```
GF_SECURITY_ADMIN_PASSWORD=admin
GF_USERS_ALLOW_SIGN_UP=false
```

In addition sample dashboards are available under (grafana/dashboards)
	- node-exporter-server-metrics.json
	- docker-dashboard.json (source = https://grafana.com/dashboards/179)

## Run the stack
From the /prometheus/components project directory run the following command:

	$ HOSTNAME=$(hostname) docker stack deploy -c docker-compose.yml prometheus


The 'docker stack deploy' command deploys the entire Grafana and Prometheus stack automatically to the Docker Swarm. By default cAdvisor and node-exporter are set to Global deployment which means they will propogate to every docker host attached to the Swarm.

In order to check the status of the newly created stack:

    $ docker stack ps prometheus

View running services:

    $ docker service ls

View logs for a specific service

    $ docker service logs prometheus_<service_name>