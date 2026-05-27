# Prometheus and Grafana in Kubernetes

Running applications in Kubernetes is not enough.

In real production environments, DevOps teams must continuously monitor:

* application health
* CPU usage
* memory usage
* network traffic
* container crashes
* node health
* response times
* errors
* cluster performance

Without monitoring:

* problems remain invisible
* failures become difficult to detect
* troubleshooting becomes very hard

This is why monitoring is one of the most critical parts of modern DevOps and Kubernetes environments.

Two of the most popular tools used for Kubernetes monitoring are:

# Prometheus

and

# Grafana

These tools are industry standards for cloud-native monitoring.

---

# Why Monitoring is Important

Imagine a production application suddenly becomes slow.

Without monitoring:

* you do not know what failed
* you cannot see CPU spikes
* memory issues remain hidden
* users complain before engineers notice

Monitoring helps engineers:

* detect problems early
* troubleshoot issues quickly
* improve performance
* maintain reliability

---

# Real-Life Example

Imagine driving a car.

Dashboard indicators show:

* speed
* fuel level
* engine temperature
* warnings

Without dashboard monitoring:

* engine could overheat
* fuel may run out
* problems remain unnoticed

Prometheus and Grafana work like:

# Kubernetes system dashboard

---

# What is Prometheus?

Prometheus is an open-source monitoring and alerting system originally developed by:

* SoundCloud

Now it is one of the most popular CNCF projects.

---

# Main Purpose of Prometheus

Prometheus collects:

* metrics
* performance data
* monitoring information

from:

* Kubernetes clusters
* applications
* servers
* containers
* databases

---

# Simple Definition

> Prometheus is a monitoring system that collects and stores metrics from applications and infrastructure.

---

# What are Metrics?

Metrics are numerical values representing system behavior.

Examples:

| Metric        | Example       |
| ------------- | ------------- |
| CPU Usage     | 70%           |
| Memory Usage  | 4GB           |
| Request Count | 1200 requests |
| Response Time | 200ms         |
| Disk Usage    | 80%           |

---

# Prometheus Architecture

Prometheus works using:

# Pull-Based Monitoring

This is extremely important.

---

# Pull Model

Prometheus periodically:

* contacts targets
* pulls metrics
* stores them internally

---

# Architecture Flow

```text id="prom1"
Application / Kubernetes Node
            ↓
        Metrics Endpoint
            ↓
        Prometheus Scrapes Data
            ↓
        Stores Metrics
```

---

# What Does "Scrape" Mean?

Scraping means:

> Prometheus requests metrics data from targets at regular intervals.

Example:

* every 15 seconds
* every 30 seconds

---

# Metrics Endpoint

Applications expose metrics using:

* HTTP endpoints

Usually:

```text id="prom2"
/metrics
```

Example:

```text id="prom3"
http://app:8080/metrics
```

---

# Example Metrics Output

```text id="prom4"
http_requests_total 1200
cpu_usage 75
memory_usage 4096
```

Prometheus collects and stores this data.

---

# Time-Series Database

Prometheus stores data as:

# Time-Series Data

Meaning:

* metrics stored with timestamps

Example:

| Time  | CPU Usage |
| ----- | --------- |
| 10:00 | 40%       |
| 10:05 | 60%       |
| 10:10 | 75%       |

This helps analyze:

* trends
* spikes
* historical performance

---

# Prometheus Components

---

# 1. Prometheus Server

Core monitoring engine.

Responsible for:

* scraping metrics
* storing metrics
* querying metrics

---

# 2. Exporters

Many systems cannot directly expose Prometheus metrics.

Exporters convert system data into Prometheus format.

---

# Common Exporters

| Exporter           | Purpose                   |
| ------------------ | ------------------------- |
| Node Exporter      | Linux server metrics      |
| kube-state-metrics | Kubernetes object metrics |
| Blackbox Exporter  | Endpoint probing          |
| MySQL Exporter     | MySQL metrics             |

---

# Example — Node Exporter

Node Exporter exposes:

* CPU usage
* RAM usage
* disk metrics
* network metrics

from Linux servers.

---

# 3. Alertmanager

Alertmanager handles:

* alerts
* notifications
* email alerts
* Slack alerts
* routing

---

# Example Alert

If CPU exceeds:

```text id="prom5"
90%
```

Prometheus can send alert.

---

# 4. PromQL

Prometheus has its own query language:

# PromQL

Used for:

* querying metrics
* calculations
* dashboards
* alerts

---

# Example PromQL Query

```text id="prom6"
rate(http_requests_total[5m])
```

Calculates:

* request rate over last 5 minutes

---

# What is Grafana?

Grafana is a visualization and dashboard platform.

Grafana itself usually does NOT collect metrics.

Instead:

* it connects to data sources
  like:
* Prometheus
* Elasticsearch
* Loki
* InfluxDB

---

# Main Purpose of Grafana

Grafana converts raw monitoring data into:

* dashboards
* graphs
* charts
* visual analytics

---

# Simple Definition

> Grafana visualizes monitoring data using dashboards and graphs.

---

# Real-Life Analogy

If Prometheus is:

* data collector

then Grafana is:

* visual dashboard screen

---

# Prometheus + Grafana Relationship

```text id="prom7"
Applications / Kubernetes
          ↓
      Prometheus
   (collect metrics)
          ↓
        Grafana
   (visualize metrics)
```

---

# What Can Grafana Show?

Grafana dashboards can display:

* CPU usage graphs
* memory usage
* Kubernetes Pod status
* network traffic
* API response times
* database metrics
* node health
* error rates

---

# Why Grafana Is Popular

Grafana provides:

* beautiful dashboards
* real-time monitoring
* interactive charts
* alert visualization
* multiple data sources

---

# Kubernetes Monitoring with Prometheus

Prometheus is heavily used in Kubernetes because Kubernetes generates huge amounts of metrics.

---

# Kubernetes Components Monitored

Prometheus can monitor:

| Component   | Metrics              |
| ----------- | -------------------- |
| Nodes       | CPU, RAM, Disk       |
| Pods        | Resource usage       |
| Deployments | Replica status       |
| API Server  | Request metrics      |
| kubelet     | Container metrics    |
| etcd        | Database performance |

---

# kube-state-metrics

kube-state-metrics exposes Kubernetes object information.

Examples:

* Pod status
* Deployment replicas
* StatefulSet health
* Namespace information

---

# cAdvisor

Kubernetes also uses:

# cAdvisor

for container resource metrics.

It provides:

* container CPU usage
* memory usage
* filesystem metrics

---

# Installing Prometheus and Grafana

In Kubernetes, these tools are commonly installed using:

# Helm Charts

Very common setup:

```bash id="prom8"
helm install monitoring prometheus-community/kube-prometheus-stack
```

---

# What This Installs

Usually includes:

* Prometheus
* Grafana
* Alertmanager
* Node Exporter
* kube-state-metrics

---

# Accessing Grafana

After installation:

```bash id="prom9"
kubectl get svc
```

Find Grafana service.

---

# Port Forward Example

```bash id="prom10"
kubectl port-forward svc/monitoring-grafana 3000:80
```

Access:

```text id="prom11"
http://localhost:3000
```

---

# Default Grafana Login

Often:

```text id="prom12"
Username: admin
Password: prom-operator
```

May vary depending on installation.

---

# Grafana Dashboards

Grafana uses:

# Dashboards

A dashboard contains:

* panels
* charts
* graphs
* metrics visualizations

---

# Example Kubernetes Dashboard

A Kubernetes dashboard may show:

| Panel             | Purpose                |
| ----------------- | ---------------------- |
| Cluster CPU Usage | Node health            |
| Pod Memory Usage  | Application monitoring |
| Pod Restart Count | Stability monitoring   |
| Network Traffic   | Traffic analysis       |

---

# Alerts in Prometheus

Monitoring alone is not enough.

Teams also need:

# Alerts

---

# Example Alerts

| Condition            | Alert         |
| -------------------- | ------------- |
| CPU > 90%            | Warning       |
| Pod CrashLoopBackOff | Critical      |
| Node Down            | Emergency     |
| Disk Full            | High Priority |

---

# Alert Flow

```text id="prom13"
Prometheus Detects Problem
          ↓
      Alertmanager
          ↓
Email / Slack / PagerDuty
```

---

# Service Discovery in Kubernetes

One of Prometheus’ biggest strengths:

# Automatic Service Discovery

Prometheus automatically discovers:

* Pods
* Services
* Nodes

inside Kubernetes.

No manual configuration required for every target.

---

# Prometheus Operator

Large Kubernetes environments often use:

Prometheus Operator

It simplifies:

* deployment
* scaling
* monitoring configuration

using:

* CRDs
* Operators

---

# Important Prometheus CRDs

Examples:

| CRD            | Purpose              |
| -------------- | -------------------- |
| ServiceMonitor | Monitor services     |
| PodMonitor     | Monitor Pods         |
| Prometheus     | Prometheus instances |
| Alertmanager   | Alert management     |

---

# Example ServiceMonitor

```yaml id="prom14"
apiVersion: monitoring.coreos.com/v1
kind: ServiceMonitor
```

This allows Prometheus to automatically scrape Kubernetes Services.

---

# Prometheus Storage

Prometheus stores metrics locally.

In production:

* persistent storage is required

Usually:

* Persistent Volumes (PV)
* StatefulSets

are used.

---

# Limitations of Prometheus

Prometheus is powerful but has limitations.

---

# 1. Long-Term Storage

Prometheus local storage is limited.

Large companies use:

* Thanos
* Cortex
* VictoriaMetrics

for long-term scalable monitoring.

---

# 2. High Cardinality Issues

Too many unique metrics can:

* increase memory usage
* overload Prometheus

---

# 3. Single Node Scaling

One Prometheus instance has scalability limits.

---

# Best Practices

---

# 1. Monitor Everything Important

Monitor:

* infrastructure
* applications
* Kubernetes components

---

# 2. Create Meaningful Alerts

Avoid alert spam.

---

# 3. Use Dashboards Properly

Dashboards should:

* quickly reveal problems
* remain simple and readable

---

# 4. Secure Monitoring Stack

Protect:

* Grafana access
* Prometheus endpoints

using:

* authentication
* RBAC
* Ingress security

---

# 5. Use Persistent Storage

Metrics should survive restarts.

---

# Real Production Monitoring Stack

Modern Kubernetes environments often use:

| Tool         | Purpose            |
| ------------ | ------------------ |
| Prometheus   | Metrics collection |
| Grafana      | Visualization      |
| Loki         | Logs               |
| Tempo        | Tracing            |
| Alertmanager | Alerts             |

Together they form:

# Observability Stack

---

# Monitoring vs Observability

---

# Monitoring

Shows:

* known issues
* system metrics

---

# Observability

Helps understand:

* why issues happened
* unknown problems
* deep system behavior

---

# Architecture Summary

```text id="prom15"
Kubernetes Cluster
        ↓
Prometheus Collects Metrics
        ↓
Stores Time-Series Data
        ↓
Grafana Visualizes Data
        ↓
Alertmanager Sends Alerts
```

---

# Final Summary

Prometheus and Grafana are industry-standard Kubernetes monitoring tools.

Prometheus:

* collects metrics
* stores time-series data
* triggers alerts

Grafana:

* visualizes metrics
* creates dashboards
* provides analytics

Important concepts:

| Concept    | Purpose                   |
| ---------- | ------------------------- |
| Metrics    | Numerical monitoring data |
| Scraping   | Pulling metrics           |
| PromQL     | Query language            |
| Exporters  | Convert metrics           |
| Dashboards | Visual monitoring         |
| Alerts     | Problem notifications     |

Prometheus and Grafana are essential for:

* Kubernetes monitoring
* DevOps observability
* production reliability
* cloud-native operations
* enterprise infrastructure management

---
