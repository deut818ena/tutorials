# How to Install Prometheus on Kubernetes Cluster?

[YouTube Tutorial](https://youtu.be/)

## Steps

- Download `prometheus-values.yaml` file for **Prometheus** from [here](https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-prometheus-stack)
- Update following variables

```yaml
etcd: false
kubeScheduler: false
adminPassword: test123
kubeControllerManager: 
  enabled: false
kubeEtcd: 
  enabled: false
kubeScheduler: 
  enabled: false
serviceMonitorSelector: 
  matchLabels: 
    prometheus: devops
commonLabels:
  prometheus: devops
```

```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
```
```bash
helm repo update
```
```bash
helm search repo kube-prometheus-stack
```

```bash
helm install monitoring \
prometheus-community/kube-prometheus-stack \
--values prometheus-values.yaml \
--version 16.10.0 \
--namespace monitoring \
--create-namespace
```

```bash
kubectl get pods -n monitoring
```
```bash
kubectl get svc -n monitoring 
```
```bash
kubectl port-forward svc/monitoring-kube-prometheus-prometheus 9090 -n monitoring
```

- Go to `http://localhost:9090` and select `targets`

- Download `postgres-values.yaml` file for **Postgres** from [here](https://github.com/bitnami/charts/tree/master/bitnami/postgresql)

## Clean Up
```bash
helm repo remove prometheus-community
```