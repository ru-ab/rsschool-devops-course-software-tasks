# Task 7: Prometheus Deployment on K8s

This repository contains a Jenkinsfile for automating the deployment of Prometheus.

## Deploy

### Prometheus Server

The deployment of Prometheus uses the Helm chart `bitnami/prometheus`. The `values.yaml` file contains all the settings for the Prometheus chart. After deployment, the Prometheus UI is available on port 8000.

### Exporters for the Kubernetes Cluster

To collect metrics from the Kubernetes cluster, two exporters are used:

- **bitnami/node-exporter**: Collects all the necessary information from the virtual machine hosting the Kubernetes cluster.

- **prometheus-community/kube-state-metrics**: Collects all metrics related to Kubernetes itself.
