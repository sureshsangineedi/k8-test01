# 🧩 Grafana and Prometheus — Installed Agents and Components

This document explains what agents and components are installed when you set up **Grafana** and **Prometheus**, both individually and as part of a Kubernetes stack.

---

## 🚀 1. Prometheus Installation

Prometheus is a **monitoring and alerting toolkit** that collects and stores time-series data from various systems using exporters (agents).

### 🔹 Core Components Installed

| Component | Description |
|------------|--------------|
| **Prometheus Server** | The main service that scrapes (collects) metrics from exporters and stores them in a time-series database. |
| **Alertmanager** *(optional)* | Sends alerts via email, Slack, or other integrations when alerting rules are triggered. |
| **Promtool** | CLI tool for checking configuration files and alerting rules. |

---

### 🔹 Common Exporters (Agents)

Exporters act as **agents** that expose system or application metrics in a Prometheus-readable format.

| Exporter / Agent | Purpose |
|------------------|----------|
| **Node Exporter** | Monitors system-level metrics such as CPU, memory, disk, and network. |
| **cAdvisor** | Provides container-level metrics (commonly used in Docker and Kubernetes). |
| **Kube-State-Metrics** | Exposes metrics about Kubernetes objects like Pods, Deployments, and Services. |
| **Blackbox Exporter** | Checks endpoint availability via HTTP, TCP, ICMP, and DNS. |
| **MySQL / PostgreSQL Exporter** | Provides database performance and query metrics. |

🟢 In **Kubernetes**, these exporters are usually deployed as **DaemonSets** or **sidecar containers** on nodes.

---

## 📊 2. Grafana Installation

Grafana is a **visualization and dashboarding tool** that connects to Prometheus (and other data sources) to display metrics.

### 🔹 Core Components Installed

| Component | Description |
|------------|--------------|
| **Grafana Server** | The main web UI where you log in, create dashboards, and visualize metrics. |
| **Grafana Agent** *(optional)* | A lightweight agent that collects metrics, logs, and traces, and forwards them to Prometheus, Loki, or Tempo. |

When installing **Grafana standalone**, only the **Grafana server** is installed.  
The **Grafana Agent** is optional and used in advanced setups or Grafana Cloud environments.

---

## ⚙️ 3. Kubernetes (Helm / kube-prometheus-stack)

If you install Grafana and Prometheus using Helm (e.g., `kube-prometheus-stack`), multiple agents and services are deployed automatically.

| Component | Purpose |
|------------|----------|
| `prometheus-server` | Main Prometheus instance for metric scraping and storage. |
| `alertmanager` | Handles alerts and notification routing. |
| `node-exporter` | Collects node-level system metrics. |
| `kube-state-metrics` | Collects metrics about Kubernetes resources and their state. |
| `grafana` | Dashboard UI for data visualization. |
| `prometheus-pushgateway` *(optional)* | Receives metrics from short-lived batch jobs. |
| `grafana-agent` *(optional)* | Collects metrics/logs/traces for Grafana Cloud. |
| `serviceMonitors` | Define which services Prometheus should scrape metrics from. |

---

## 🧠 Summary

| Tool | Main Component | Agents Installed |
|------|----------------|------------------|
| **Prometheus** | `prometheus-server` | `node-exporter`, `cadvisor`, `kube-state-metrics`, etc. |
| **Grafana** | `grafana-server` | `grafana-agent` *(optional)* |
| **Kubernetes Stack** | Combined setup | Includes Prometheus, Grafana, Alertmanager, Exporters, and Agents |

---

✅ **In Short:**
- Prometheus → Collects metrics via exporters/agents  
- Grafana → Visualizes the collected metrics  
- Helm Stack → Installs everything together automatically

---
