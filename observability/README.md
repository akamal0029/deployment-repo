# Observability Setup

This folder installs observability for the `fastapi` namespace only:

- Prometheus and Grafana through `kube-prometheus-stack`.
- Loki and Promtail for pod logs.
- A custom Grafana dashboard for FastAPI pods, services, request metrics, and logs.

## Install

```bash
kubectl create namespace observability

helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update

helm upgrade --install observability prometheus-community/kube-prometheus-stack \
  --namespace observability \
  -f observability/kube-prometheus-stack-values.yaml

helm upgrade --install loki grafana/loki-stack \
  --namespace observability \
  -f observability/loki-stack-values.yaml

kubectl apply -f observability/fastapi-dashboard-configmap.yaml
```

## Open Grafana

```bash
kubectl port-forward svc/observability-grafana -n observability 3000:80
```

Open `http://localhost:3000`.

Login:

```text
username: admin
password: admin
```

Open the dashboard:

```text
Dashboards > FastAPI Namespace Observability
```

The logs panel can filter by:

- `fastapi-app`
- `fastapi-secondary`

## Generate Test Logs

```bash
kubectl port-forward svc/fastapi-app -n fastapi 8000:80
curl http://127.0.0.1:8000/
curl http://127.0.0.1:8000/version
```

For the second service:

```bash
kubectl port-forward svc/fastapi-app-secondary -n fastapi 8001:80
curl http://127.0.0.1:8001/
curl http://127.0.0.1:8001/workload
```
