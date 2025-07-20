### Bug Report Template

# This issue template helps you create a clear, comprehensive bug report for your k8s-eks-gitops project.
name: "🐛 Bug report"
about: "Create a report to help us improve the EKS GitOps Deployment"
title: "[BUG] "
labels: ["bug"]
assignees: []
---

**Describe the bug**
A clear and concise description of what the bug is.

**To Reproduce**
Steps to reproduce the behavior:
1. Make sure you have deployed the manifests in `infra/overlays/prod`.
2. Run `kubectl apply -f k8s_orchestration.yaml` or relevant overlay.
3. Observe the error or unexpected behavior.

**Expected behavior**
What you expected to happen.

**Screenshots or Logs**
If applicable, add screenshots or copy relevant pod logs:
```
kubectl logs -n my-app <pod-name>
```

**Environment:**
- EKS version (e.g. `1.24`):
- Helm/Kustomize version:
- GitHub Actions workflow SHA:
- Argo CD revision:

**Additional context**
Add any other context about the problem here.
``` 