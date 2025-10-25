# ArgoCD Apps-of-Apps Architecture with Nginx

This repository contains a simple ArgoCD apps-of-apps pattern with an nginx application.

## Structure

```
.
├── argocd/
│   ├── root-app.yaml           # Root application (apps-of-apps)
│   └── apps/
│       ├── kustomization.yaml  # Kustomization for all child apps
│       └── nginx-app.yaml      # Nginx application definition
└── apps/
    └── nginx/
        ├── kustomization.yaml  # Nginx kustomization
        ├── namespace.yaml      # Nginx namespace
        ├── deployment.yaml     # Nginx deployment
        └── service.yaml        # Nginx service
```

## Architecture Overview

This follows the **Apps-of-Apps** pattern:
- **Root App** (`root-app.yaml`) manages all child applications
- **Child Apps** (like `nginx-app.yaml`) manage actual workloads
- Each application is self-contained and can be synced independently

## Setup Instructions

### 1. Update Repository URLs

Before deploying, update the `repoURL` in these files with your Git repository:
- [argocd/root-app.yaml](argocd/root-app.yaml)
- [argocd/apps/nginx-app.yaml](argocd/apps/nginx-app.yaml)

### 2. Deploy the Root Application

```bash
# Make sure you have ArgoCD installed and are logged in
kubectl apply -f argocd/root-app.yaml
```

### 3. Verify Deployment

```bash
# Check ArgoCD applications
argocd app list

# Check nginx pods
kubectl get pods -n nginx

# Check nginx service
kubectl get svc -n nginx
```

## How It Works

1. **Root Application** monitors `argocd/apps/` directory
2. When it finds child application manifests (like `nginx-app.yaml`), it creates those applications
3. Each child application monitors its own path (like `apps/nginx/`) and deploys resources
4. All applications have automated sync enabled for GitOps workflow

## Adding More Applications

To add a new application:

1. Create a new directory under `apps/` (e.g., `apps/myapp/`)
2. Add Kubernetes manifests and kustomization.yaml
3. Create an ArgoCD Application manifest in `argocd/apps/myapp-app.yaml`
4. Add the new app to `argocd/apps/kustomization.yaml`
5. Commit and push - ArgoCD will automatically sync!

## Features

- **Automated Sync**: Applications automatically sync when changes are pushed
- **Self-Healing**: ArgoCD will correct any drift from Git
- **Namespace Management**: Namespaces are automatically created
- **Scalable**: Easy to add more applications following the same pattern
