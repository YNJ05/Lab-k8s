# Kubernetes Monitoring Lab

A Kubernetes monitoring and observability lab demonstrating metric collection, visualization, and alerting using **Prometheus**, **Alertmanager**, and **Grafana** via the Prometheus Operator (`kube-prometheus-stack`).

---

## 📌 Description

This lab sets up an end-to-end observability stack on Kubernetes:

- **Custom Application Metrics**: A Node.js (Express) app instrumented with `prom-client` exposing custom metrics (`/metrics`) for request count, latency, and active connections.
- **NGINX Exporter**: Scrapes NGINX `stub_status` and exports metrics to Prometheus.
- **ServiceMonitors**: Custom Resource Definitions (CRDs) allowing Prometheus Operator to automatically discover and scrape targets.
- **Alerting Rules**: Custom `PrometheusRule` configurations detecting application downtime, high 5xx error rates, latency spikes (p95 > 1s), and crash loops.
- **Alertmanager & Webhooks**: Routes alerts to a webhook logger (`http-https-echo`) for notification validation.
- **Stress & Failure Simulation**: Uses a `memory-hog` stress pod to test memory limits and OOMKilled alerts.

---

## 📂 Structure

- `k8s/`: Kubernetes manifests (Deployments, Services, ServiceMonitors, Alert Rules, ConfigMaps).
- `nodejs-app/`: Node.js application, Prometheus instrumentation, and Dockerfile.

---

## 🚀 Quick Run

```bash
# 1. Deploy the Node.js application
docker build -t lab-nodejs-app:v1 ./nodejs-app
kubectl apply -f k8s/lab-nodejs.yaml

# 2. Deploy NGINX & Prometheus Exporter
kubectl create deployment nginx --image=nginx
kubectl apply -f k8s/nginx-config.yaml
kubectl patch deployment nginx --patch-file k8s/nginx-patch.yaml
kubectl expose deployment nginx --port=80
kubectl apply -f k8s/nginx-exporter.yaml

# 3. Apply ServiceMonitors & Alert Rules
kubectl apply -f k8s/node-app-servicemonitor.yaml
kubectl apply -f k8s/nginx-servicemonitor.yaml
kubectl apply -f k8s/lab-alert-rules.yaml
kubectl apply -f k8s/webhook-logger.yaml
```
