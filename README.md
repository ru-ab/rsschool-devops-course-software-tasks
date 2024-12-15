# Task 8: Grafana Installation and Dashboard Creation

This repository contains a Jenkinsfile for automating the deployment of Prometheus and Grafana.

## Preparation

To deploy, you need to prepare the following in advance:

- A Kubernetes cluster - to run Prometheus in a container
- Jenkins - for CI/CD

## Basic Information

### Prometheus Server

The deployment of Prometheus uses the Helm chart `bitnami/prometheus`. The `prometheus-values.yaml` file contains all the settings for the Prometheus chart. After deployment, the Prometheus UI is available on port `8000`.

### Exporters for the Kubernetes Cluster

To collect metrics from the Kubernetes cluster, two exporters are used:

- **bitnami/node-exporter**: Collects all the necessary information from the virtual machine hosting the Kubernetes cluster.

- **prometheus-community/kube-state-metrics**: Collects all metrics related to Kubernetes itself.

### Grafana

The Grafana deployment uses the Helm chart `bitnami/grafana`. The `grafana-values.yaml` file contains all the configuration settings for the Grafana chart. After deployment, the Grafana UI is available on port `3000`.

#### Administrator Password

To set the administrator password, you need to create credentials in Jenkins with the ID `GRAFANA_ADMIN_PASSWORD` and the administrator password.

#### Prometheus Data Source

When Grafana is deployed, the Prometheus server will automatically be added as a data source from the configuration in the `datasources-secret.yaml` file.

#### Custom Dashboard

A custom dashboard will be automatically added to Grafana during deployment. The dashboard configuration is located in the `node-dashboard.json` file. The dashboard consists of 3 panels. You can also create the dashboard manually, here configuration:

- **CPU Usage %**
  - Visualization: Time Series
  - Query:

```
avg without (mode,cpu) ((
  1 - rate(node_cpu_seconds_total{mode="idle"}[2m])) * 100
)
```

- **Disk Usage %**
  - Visualization: Stat
  - Show percent change: true
  - Threshold:
    - Yellow: 80
    - Red: 90
  - Query:

```
((node_filesystem_size_bytes - node_filesystem_avail_bytes{mountpoint="/"}) / node_filesystem_size_bytes) * 100
```

- **Free Memory (MB)**
  - Visualization: Gauge
  - Query:

```
node_memory_MemFree_bytes / (1024 * 1024)
```

#### Alerting Configuration

Email notifications are configured using the Sendgrid service, with the SMTP configuration specified in the `grafana-values.yaml` file.

Email notifications are triggered for the following events:

- CPU usage exceeds 80%
- Free memory is less than 500MB

The configuration for the Contact Point and Alert Rules is provided in the `grafana-alerting.yaml` file.

## How to Deploy

To deploy, create a new Pipeline in Jenkins and select "Script from SCM," specifying the repository link https://github.com/ru-ab/rsschool-devops-course-software-tasks.git. Additionally, set the branch to `task_9`.

After that, you can either run the Pipeline manually or configure automatic triggers for execution.
