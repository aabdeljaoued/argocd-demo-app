# argocd-demo-app

A minimal set of Kubernetes manifests for demonstrating ArgoCD application deployment. Deploys an nginx web server with a ConfigMap, Secret, and ClusterIP service in the `demo` namespace.

## Resources

| Resource       | Kind        | Description                                      |
| -------------- | ----------- | ------------------------------------------------ |
| `deployment.yaml` | Deployment | Runs **nginx:1.25** with 10 replicas             |
| `service.yaml`    | Service    | ClusterIP exposing port 80                        |
| `configmap.yaml`  | ConfigMap  | Injects env vars (`PORT=3306`, `URL=localhost`)   |
| `secret.yaml`     | Secret     | Provides a password (`secret`) mounted at `/etc/foo` |

## Architecture

- The **Deployment** (`nginx-demo`) creates 10 nginx pods. Each pod:
  - Injects environment variables from `my-configmap` via `envFrom`
  - Mounts the `my-secret` volume at `/etc/foo` (read-only)
- The **Service** (`nginx-demo`) exposes port 80 as a ClusterIP.
- All resources share the `demo` namespace.

## Usage

```bash
# Create the namespace and deploy
kubectl create namespace demo
kubectl apply -f configmap.yaml -f secret.yaml -f deployment.yaml -f service.yaml

# Verify
kubectl -n demo get pods,svc
```

## ArgoCD Sync

Point ArgoCD to this repository and define an Application with:

- **Project**: default
- **Sync policy**: Automatic (or Manual)
- **Path**: `.`
- **Namespace**: `demo`

The manifests are self-contained and compatible with both `kubectl apply` and ArgoCD.
