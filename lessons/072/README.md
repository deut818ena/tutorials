# How to Install Prometheus on Kubernetes Cluster?

[YouTube Tutorial](https://youtu.be/)


## Steps
1. Download values.yaml file - https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-prometheus-stack

2. eksctl create cluster -f eks.yaml --ssh-access --ssh-public-key=~/.ssh/id_rsa.pub

change - serviceMonitorSelector
disable etcd, scheduler, controller-manager
kubectl run ubuntu -it -n monitoring --rm --image=ubuntu:20.04 -- bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm search repo prometheus-community/kube-prometheus-stack
helm install prometheus-stack prometheus-community/kube-prometheus-stack --version 16.10.0 --namespace monitoring --create-namespace
kubectl get pods -n monitoring
kubectl get svc -n monitoring
kubectl port-forward svc/prometheus-stack-kube-prom-prometheus 9090 -n monitoring