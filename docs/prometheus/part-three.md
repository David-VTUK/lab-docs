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

For this install, each node will expose the `node-exporter` metrics endpoint as a `nodeport` service type.

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

Install `node-exporter` leveraging the customisations specified in the previous step.

```bash
helm install prom-stack-node-exporter prometheus-community/prometheus-node-exporter \
--namespace prom-stack \
--create-namespace \
-f values.yaml
```