# Kube-prometheus Stack Install

## Prerequisites

* `helm` CLI
* `kubectl` CLI
* `kubeconfig` file for the monitoring cluster

## 1. Add Helm Chart Repo

This will be used to install the `kube-prometheus` stack.

```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
```

## 2. Prepare `values.yaml`

```yaml
alertmanager:
  ingress:
    annotations:
      kubernetes.io/ingress.class: nginx
      nginx.ingress.kubernetes.io/rewrite-target: /
      cert-manager.io/cluster-issuer: letsencrypt-production
    enabled: true
    hosts:
      - alertmanager-lab.virtualthoughts.co.uk
    paths:
      - /
    tls:
      - hosts:
          - alertmanager-lab.virtualthoughts.co.uk
        secretName: alertmanager-lab-tls
    pathType: Prefix
prometheus:
  prometheusSpec:
    storageSpec:
      volumeClaimTemplate:
        spec:
          accessModes:
            - ReadWriteOnce
          resources:
            requests:
              storage: 50Gi
          selector:
          volumeMode: Filesystem
  ingress:
    annotations:
      kubernetes.io/ingress.class: nginx
      nginx.ingress.kubernetes.io/rewrite-target: /
      cert-manager.io/cluster-issuer: letsencrypt-production
    enabled: true
    hosts:
      - prometheus-lab.virtualthoughts.co.uk
    paths:
      - /
    tls:
      - hosts:
          - prometheus-lab.virtualthoughts.co.uk
        secretName: prometheus-lab-tls
    pathType: Prefix
grafana:
  ingress:
    annotations:
      kubernetes.io/ingress.class: nginx
      nginx.ingress.kubernetes.io/rewrite-target: /
      cert-manager.io/cluster-issuer: letsencrypt-production
    enabled: true
    hosts:
      - grafana-lab.virtualthoughts.co.uk
    paths:
      - /
    tls:
      - hosts:
          - grafana-lab.virtualthoughts.co.uk
        secretName: grafana-lab-tls
    pathType: Prefix
  persistence:
    size: 5Gi
    subPath: null
    type: statefulset
    enabled: true
```

!!! note

    The values.yaml file provides a number of customisations. Namely leveraging cert-manager for TLS certificate
    management, exposing Prometheus, Grafana and Alertmanager via Ingress, and enabling persistent storage.

## 3. Install Chart

With the customisations complete, these will be used to customise the installation. 

```bash
helm install prom-stack-lab prometheus-community/kube-prometheus-stack \
  --namespace prom-stack \
  --create-namespace \
  -f values.yaml
```