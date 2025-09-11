# GitOps for EKS 3-Tier App

This repo contains the **GitOps setup for the EKS 3-tier application** using **Argo CD App-of-Apps**.

It works together with the [`eks-3tier-app`](https://github.com/your-actual-username/eks-3tier-app) repo, which has Terraform, Helm charts, and Dockerfiles.  
This repo is only for **Argo CD Applications, values, and supporting manifests**.

---

## Repo Layout
```
applications/        # Argo CD Applications (root + child apps)
  ├─ app-of-apps.yaml
  ├─ auth-service-app.yaml
  ├─ frontend-app.yaml
  ├─ profile-service-app.yaml
  ├─ external-secrets-app.yaml
  ├─ clustersecretstore-app.yaml
  ├─ fluent-bit-app.yaml
  ├─ logging-cloudwatch-app.yaml
  ├─ kube-prometheus-crds-app.yaml
  ├─ kube-prometheus-app.yaml
  └─ ...
apps/                # Helm charts for microservices
  ├─ auth/
  ├─ frontend/
  └─ profile/
infra/cluster-infra/ # cluster-level configs (e.g. storageclass)
logging/             # values for logging setup
observability/       # monitoring configs (alerts, prometheus, grafana)
```

---

## How to Use
1. Install Argo CD:
   ```bash
   kubectl create ns argocd
   helm repo add argo https://argoproj.github.io/argo-helm
   helm install argocd argo/argo-cd -n argocd
   ```

2. Apply the root app:
   ```bash
   kubectl apply -n argocd -f applications/app-of-apps.yaml
   ```

Argo CD will sync all apps defined under `applications/`.

---

## Releasing a New Version
- Update the image tag in `apps/<service>/values.yaml`
- Commit & push
- Argo CD auto-syncs to the cluster

---

## Includes
- **Services:** frontend, auth-service, profile-service
- **Secrets:** External Secrets Operator + ClusterSecretStore
- **Observability:** Prometheus, Grafana, Alerts
- **Logging:** Fluent Bit → CloudWatch
- **Cluster Infra:** gp3 storageclass and other base configs

---

## Notes
- No plaintext secrets in repo → only **ExternalSecret** references
- Ordering handled with separate `*-crds-app.yaml` where needed
- Cross-repo link: workloads live in [`eks-3tier-app`](https://github.com/your-actual-username/eks-3tier-app)
