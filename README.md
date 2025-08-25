# observability_prometheus-grafana

Step 1: Prerequisites

Kubernetes cluster (min 1 master + 1 node)
kubectl configured
helm installed
Namespace for monitoring:

kubectl create namespace monitoring

Step 2: Add Helm Repositories
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update

Step 3: Deploy Prometheus

( We will use kube-prometheus-stack, which includes Prometheus, Alertmanager, and node exporters.)

helm install kube-prometheus prometheus-community/kube-prometheus-stack -n monitoring \
  --set prometheus.prometheusSpec.retention=7d \
  --set prometheus.prometheusSpec.scrapeInterval=30s


This installs Prometheus with a 7-day retention and 30s scrape interval.
Check pods:

kubectl get pods -n monitoring

Step 4: Deploy Grafana

The kube-prometheus-stack also installs Grafana, but you can also install it separately:

helm install grafana grafana/grafana -n monitoring \
  --set adminUser=admin \
  --set adminPassword=admin123 \
  --set service.type=NodePort

Check service to get Grafana NodePort:

  kubectl get svc -n monitoring

Access Grafana: http://<NodeIP>:<NodePort>

Login: admin/admin123

Step 5: Add Prometheus as Grafana Data Source

If Grafana installed separately:
Go to Grafana → Configuration → Data Sources → Add data source
Choose Prometheus
URL: http://kube-prometheus-prometheus.monitoring.svc.cluster.local:9090
Save & Test

Step 6: Optional Dashboards
Import Kubernetes dashboards from Grafana Grafana Dashboard IDs
Examples:
Cluster monitoring: 315
Node metrics: 1860
