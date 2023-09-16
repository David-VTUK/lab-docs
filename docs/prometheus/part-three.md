# Node-Exporter Install

## Prerequisites

* `helm` CLI
* `kubectl` CLI
* `kubeconfig` file for the monitoring cluster

## 1. Add Helm Chart Repo

```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
```

## 2. Prepare `values.yaml`

```yaml
service:
  enabled: true
  type: NodePort
  port: 9100
  targetPort: 9100
  nodePort:
  portName: metrics
```

## 3. Install Chart

```bash
helm install prom-stack-node-exporter prometheus-community/prometheus-node-exporter \
--namespace prom-stack \
--create-namespace \
-f values.yaml
```