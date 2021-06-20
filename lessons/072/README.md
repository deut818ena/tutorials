# How to Install Prometheus on Kubernetes Cluster?

[YouTube Tutorial](https://youtu.be/)


## Steps
1. Download values.yaml file - https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-prometheus-stack

change - serviceMonitorSelector
disable etcd, scheduler, controller-manager

[EKS] [request]: allow to configure metricsBindAddress for kube-proxy
https://github.com/aws/containers-roadmap/issues/657


helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm search repo prometheus-community/kube-prometheus-stack

helm install monitoring \
  prometheus-community/kube-prometheus-stack \
  --values prometheus-values.yaml \
  --version 16.10.0 \
  --namespace monitoring \
  --create-namespace

kubectl get pods -n monitoring
kubectl get svc -n monitoring
kubectl port-forward svc/monitoring-kube-prometheus-prometheus 9090 -n monitoring


kubectl get cm kube-proxy-config -n kube-system -o yaml
kubectl get pods -n kube-system
kubectl -n kube-system get cm kube-proxy-config -o yaml |sed 's/metricsBindAddress: 127.0.0.1:10249/metricsBindAddress: 0.0.0.0:10249/' | kubectl apply -f -
kubectl -n kube-system patch ds kube-proxy -p "{\"spec\":{\"template\":{\"metadata\":{\"labels\":{\"updateTime\":\"`date +'%s'`\"}}}}}"

wait 20 min

kubectl port-forward svc/monitoring-grafana -n monitoring 3000:80
kubectl port-forward svc/monitoring-kube-prom-alertmanager -n monitoring 9093



# Deploy postgress
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update
helm search repo postgres

postgres-values.yaml
https://github.com/bitnami/charts/tree/master/bitnami/postgresql

helm install postgres bitnami/postgresql -f postgres-values.yaml --version 10.5.0 --namespace db --create-namespace
9628
kubectl get servicemonitors postgres-postgresql -oyaml -n db


helm uninstall postgres -n db

kubectl get endpoints -n db
kubectl describe endpoints postgres-postgresql-metrics -n db

# Remove
helm uninstall monitoring -n monitoring

kubectl delete crd alertmanagerconfigs.monitoring.coreos.com
kubectl delete crd alertmanagers.monitoring.coreos.com
kubectl delete crd podmonitors.monitoring.coreos.com
kubectl delete crd probes.monitoring.coreos.com
kubectl delete crd prometheuses.monitoring.coreos.com
kubectl delete crd prometheusrules.monitoring.coreos.com
kubectl delete crd servicemonitors.monitoring.coreos.com
kubectl delete crd thanosrulers.monitoring.coreos.com






# Values Prometheus
commonLabels:
  prometheus: devops

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