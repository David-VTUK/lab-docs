# Recording Rules 

Recording rules enable us to precompute frequently used expressions and save their result as a new time series.

## CPU's per node:

```yaml
apiVersion: monitoring.coreos.com/v1
kind: PrometheusRule
metadata:
  name: node-cpu-count-recording-rule
  namespace: prom-stack
  labels:
    release: prom-stack-lab
spec:
  groups:
    - name: node-cpu-count-group
      rules:
        - record: lab_node:total_cpu_count
          expr: count without(cpu, mode) (node_cpu_seconds_total{mode="idle", cluster="workload-cluster"})
```

## RAM amount in GB Per Node

```yaml
apiVersion: monitoring.coreos.com/v1
kind: PrometheusRule
metadata:
  name: node-total-mem-gb
  namespace: prom-stack
  labels:
    release: prom-stack-lab
spec:
  groups:
    - name: node-total-mem-gb
      rules:
        - record: lab_node:total_memory_gb
          expr: (node_memory_MemTotal_bytes{cluster="workload-cluster"} / 1024^3)
```

## Ratio of CPU:RAM

```yaml
apiVersion: monitoring.coreos.com/v1
kind: PrometheusRule
metadata:
  name: node-mem-cpu-ratio
  namespace: prom-stack
  labels:
    release: prom-stack-lab
spec:
  groups:
    - name: node-mem-cpu-ratio
      rules:
        - record: lab_node:mem_cpu_ratio
          expr: (node_memory_MemTotal_bytes{cluster="workload-cluster"} / 1024^3) / count without(cpu, mode) (node_cpu_seconds_total{mode="idle", cluster="workload-cluster"})
```

# Visualising with Grafana

Example of basic graphs leveraging the above recording rules:

![img.png](../Images/grafana-dashboard.png)