# Task 7: Prometheus Deployment on K8s

This repository contains a Jenkinsfile for automating the deployment of Prometheus.

## Preparation

To deploy Prometheus, you need to prepare the following in advance:

- A Kubernetes cluster - to run Prometheus in a container
- Jenkins - for CI/CD

## Basic Information

### Prometheus Server

The deployment of Prometheus uses the Helm chart `bitnami/prometheus`. The `values.yaml` file contains all the settings for the Prometheus chart. After deployment, the Prometheus UI is available on port 8000.

### Exporters for the Kubernetes Cluster

To collect metrics from the Kubernetes cluster, two exporters are used:

- **bitnami/node-exporter**: Collects all the necessary information from the virtual machine hosting the Kubernetes cluster.

- **prometheus-community/kube-state-metrics**: Collects all metrics related to Kubernetes itself.

## How to Deploy

To deploy Prometheus, create a new Pipeline in Jenkins and select "Script from SCM," specifying the repository link https://github.com/ru-ab/rsschool-devops-course-software-tasks.git. Additionally, set the branch to `task_7`.

After that, you can either run the Pipeline manually or configure automatic triggers for execution.
