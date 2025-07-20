# EKS GitOps Deployment

**EKS GitOps Deployment**  
A fully automated, end-to-end GitOps pipeline for deploying containerized applications on Amazon EKS. This project uses **GitHub Actions** for continuous integration—building, testing, scanning, and publishing Docker images to ECR—and **Argo CD** for declarative, self-healing continuous delivery. Kustomize overlays keep your manifests DRY, while Prometheus and Grafana provide real-time monitoring and autoscaling via HPA and the Cluster Autoscaler. Secrets are managed securely (KMS-encrypted Kubernetes Secrets, SealedSecrets or ExternalSecrets), and the entire workflow is locked down with AWS IRSA, Kubernetes RBAC, Pod Security Admission, network policies, and best-practice CIS benchmarking.

## Repository Structure

```
.
├── .github
│   └── workflows
│       └── ci.yaml
├── cluster-config.yaml
├── k8s_orchestration.yaml
├── infra
│   ├── overlays
│   │   └── prod
│   │       └── kustomization.yaml
│   └── argocd
│       └── application.yaml
├── SECURITY.md
├── LICENSE
└── docs
    ├── architecture.mmd
    └── ci_cd_flow.mmd
```

## Getting Started

1. **Create EKS cluster**  
   ```bash
   eksctl create cluster -f cluster-config.yaml
   ```

2. **Install Argo CD**  
   ```bash
   kubectl create namespace argocd
   kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
   ```

3. **Deploy Argo CD Application**  
   ```bash
   kubectl apply -f infra/argocd/application.yaml
   ```

4. **Configure GitHub Secrets**  
   - `AWS_ACCOUNT_ID`: Your AWS Account ID  
   - `AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY` (for GitHub Actions role assumption)

5. **Push Changes**  
   Any changes to `app/` code or `infra/overlays/prod` manifests will automatically trigger CI and CD pipelines.

## Notes

- Ensure you have the necessary IAM roles and policies set up for IRSA and the Cluster Autoscaler.
- Customize the `k8s_orchestration.yaml` as needed for your application.
