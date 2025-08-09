
# CloudNative Bank — Task 2 (Argo CD: Declarative Cluster Management)

This repository contains the minimal manifests required to demonstrate **Task 2**:
- Argo CD connected to this Git repo
- An example Kubernetes application (`sample-app`) managed declaratively
- Support for drift detection and rollback

## Structure
```
k8s-manifests/
  namespaces/
    bank-dev-namespace.yaml
  sample-app/
    deployment.yaml
    service.yaml
    kustomization.yaml
argo/
  app-sample.yaml
```

## How to Use (Quick Start)

1) Create namespace (optional if using Argo to create it via `app-sample.yaml`):
```bash
kubectl apply -f k8s-manifests/namespaces/bank-dev-namespace.yaml
```

2) (Option A) **Create Argo CD Application using YAML**:
```bash
kubectl apply -n argocd -f argo/app-sample.yaml
```

3) (Option B) **Create Argo CD Application using CLI**:
```bash
argocd app create sample-app   --repo https://github.com/<your-username>/<your-repo>   --path k8s-manifests/sample-app   --dest-server https://kubernetes.default.svc   --dest-namespace bank-dev
```

4) **Sync & Verify**:
```bash
argocd app sync sample-app
argocd app get sample-app
```

5) **Simulate Drift** (change live replicas to 5; Git remains at 2):
```bash
kubectl -n bank-dev scale deployment sample-app --replicas=5
```

6) **Fix Drift (Sync from Git)**:
```bash
argocd app sync sample-app
```

> Image used is `ghcr.io/stefanprodan/podinfo:6.3.5` for a stable demo target.
