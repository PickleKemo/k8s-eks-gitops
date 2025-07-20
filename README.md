# EKS GitOps Deployment

This repository contains everything you need to deploy a containerized application on Amazon EKS using GitHub Actions for CI and Argo CD for GitOps-based CD.

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
└── docs
    ├── architecture.mmd
    └── ci_cd_flow.mmd
```

## Getting Started

1. **Create EKS cluster**  
   \`\`\`bash
   eksctl create cluster -f cluster-config.yaml
   \`\`\`

2. **Install Argo CD**  
   \`\`\`bash
   kubectl create namespace argocd
   kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
   \`\`\`

3. **Deploy Argo CD Application**  
   \`\`\`bash
   kubectl apply -f infra/argocd/application.yaml
   \`\`\`

4. **Configure GitHub Secrets**  
   - \`AWS_ACCOUNT_ID\`: Your AWS Account ID  
   - \`AWS_ACCESS_KEY_ID\`, \`AWS_SECRET_ACCESS_KEY\` (for GitHub Actions role assumption)

5. **Push Changes**  
   Any changes to \`app/\` code or \`infra/overlays/prod\` manifests will automatically trigger CI and CD pipelines.

## Notes

- Ensure you have the necessary IAM roles and policies set up for IRSA and the Cluster Autoscaler.
- Customize the \`k8s_orchestration.yaml\` as needed for your application.
