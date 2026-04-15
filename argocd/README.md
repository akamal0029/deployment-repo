# Kubernetes and ArgoCD Setup

Use this guide after you have a Kubernetes cluster available. The commands work with any kubeconfig-backed cluster, including kind, minikube, EKS, AKS, GKE, or a managed on-prem cluster.

## Option A: create a local kind cluster

```bash
kind create cluster --name fastapi-cicd
kubectl cluster-info --context kind-fastapi-cicd
```

## Install ArgoCD

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
kubectl wait --for=condition=available --timeout=300s deployment/argocd-server -n argocd
```

## Configure the ArgoCD application

Edit `argocd/application.yaml` and replace:

- `https://github.com/your-org/fastapi-deployment.git`
- `targetRevision` if your deployment branch is not `main`

Then apply it:

```bash
kubectl apply -f argocd/application.yaml
```

## Access ArgoCD

```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

Get the initial admin password:

```bash
kubectl -n argocd get secret argocd-initial-admin-secret \
  -o jsonpath="{.data.password}" | base64 -d
```

Open `https://localhost:8080`, sign in with username `admin`, and the password from the command above.

## Production notes

- Use a private deployment repository if cluster manifests expose sensitive topology.
- Store application secrets in an external secret manager or sealed secret workflow.
- Prefer digest-pinned images for high-compliance environments. This example uses SHA tags for readability.
- Require review on the deployment repository before merging automated promotion pull requests.
