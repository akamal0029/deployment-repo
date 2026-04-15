# FastAPI Deployment Repository

This directory is ready to become the separate deployment repository referenced by `fastapi-app-repo/.github/workflows/ci-cd.yml`.

The GitHub Actions workflow updates `helm/fastapi-app/values.yaml` in that repository and raises a pull request. After the pull request is merged, ArgoCD detects the new desired image tag and syncs it to the cluster.

## Structure

```text
helm/fastapi-app/
  Chart.yaml
  values.yaml
  templates/

argocd/
  application.yaml
  README.md
```

## Manual deployment test

```bash
helm template fastapi-app ./helm/fastapi-app
helm upgrade --install fastapi-app ./helm/fastapi-app --namespace fastapi --create-namespace
```

## ArgoCD

Update `argocd/application.yaml` with this repository's GitHub URL, then apply it to the cluster:

```bash
kubectl apply -f argocd/application.yaml
```
