# Scrape Configuration

Using the Prometheus Operator, `ServiceMonitor` and `PodMonitor` are standard abstractions for service discovery within
a Kubernetes cluster. However, in this example we are scraping metrics directly exposed from nodes and not from a 
Kubernetes service. 

Two ways to approach this include:

* Modifying the `Prometheus` yaml file and directly injecting additional scrape configs
* Modifying the `Prometheus` yaml file and reference an existing secret with `additionalScrapeConfigs`

For this example, the latter is used as a provides a less error-prone and scalable way to manage additional scrape configurations

##  Additional Scrape config

```yaml
- job_name: 'workload-cluster-nodes'
  static_configs:
  - targets:
    - '172.16.30.79:9100'
    - '172.16.30.80:9100'
    - '172.16.30.78:9100'
    labels:
      cluster: 'workload-cluster'
```

## Create Secret

`kubectl create secret generic additional-scrape-configs --from-file=node-exporter-scrape.yaml --dry-run=client -oyaml > additional-scrape-configs.yaml`

## Modify Prometheus Instance with `additionalScrapeConfigs`

```yaml
apiVersion: monitoring.coreos.com/v1
kind: Prometheus
metadata:
  labels:
    app: kube-prometheus-stack-prometheus
    app.kubernetes.io/instance: prom-stack-lab
    app.kubernetes.io/managed-by: Helm
    app.kubernetes.io/part-of: kube-prometheus-stack
    app.kubernetes.io/version: 51.0.2
    chart: kube-prometheus-stack-51.0.2
    heritage: Helm
    release: prom-stack-lab
  name: prom-stack-lab-kube-promet-prometheus
  namespace: prom-stack
spec:
  additionalScrapeConfigs:
    key: node-exporter-scrape.yaml
    name: additional-scrape-configs
```

Which results in the following:

![img.png](../Images/scrape-config.png)

## Validate Scrape Config

From the Prometheus UI, we can validate the scrape configuration:

![img.png](../Images/scrape.png)

From the Prometheus UI, we can validate metrics have been scraped with a simple `PromQL` query:

## Test Scrape Config

![img.png](../Images/prom-query.png)

