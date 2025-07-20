[![CI](https://github.com/PickleKemo/k8s-eks-gitops/actions/workflows/ci.yaml/badge.svg)](https://github.com/PickleKemo/k8s-eks-gitops/actions/workflows/ci.yaml)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Argo CD Application Status](https://img.shields.io/badge/ArgoCD-Synced-brightgreen.svg)](https://argocd.example.com/applications/my-app)
[![Trivy Vulnerabilities](https://img.shields.io/badge/Trivy-Security_passed-brightgreen.svg)](https://github.com/PickleKemo/k8s-eks-gitops/actions/workflows/ci.yaml)
[![ECR Image Scan](https://img.shields.io/badge/ECR%20Scan-passing-brightgreen)](https://console.aws.amazon.com/ecr/repositories/your-repo/image-scanning)
[![Docker Image](https://img.shields.io/docker/v/your-org/my-app?label=Docker%20Image&style=flat)](https://hub.docker.com/r/your-org/my-app)

# EKS GitOps Deployment

**EKS GitOps Deployment**  
A fully automated, end-to-end GitOps pipeline for deploying containerized applications on Amazon EKS. This project uses **GitHub Actions** for continuous integration building, testing, scanning, and publishing Docker images to ECR and **Argo CD** for declarative, self-healing continuous delivery. Kustomize overlays keep your manifests DRY, while Prometheus and Grafana provide real-time monitoring and autoscaling via HPA and the Cluster Autoscaler. Secrets are managed securely (KMS-encrypted Kubernetes Secrets, SealedSecrets or ExternalSecrets), and the entire workflow is locked down with AWS IRSA, Kubernetes RBAC, Pod Security Admission, network policies, and best-practice CIS benchmarking.

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

## IAM Roles and Policies for IRSA and Cluster Autoscaler

### 1. IRSA Role for Your Application

##### a)Trust Policy
```json
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Principal": {
      "Federated": "arn:aws:iam::123456789012:oidc-provider/oidc.eks.us-east-1.amazonaws.com/id/EXAMPLED539D4633E53DE1B716D3041E"
    },
    "Action": "sts:AssumeRoleWithWebIdentity",
    "Condition": {
      "StringEquals": {
        "oidc.eks.us-east-1.amazonaws.com/id/EXAMPLED539D4633E53DE1B716D3041E:sub": "system:serviceaccount:my-app:my-app-sa"
      }
    }
  }]
}
```
#### b) Permissions Policy
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:ListBucket"
      ],
      "Resource": [
        "arn:aws:s3:::my-app-bucket",
        "arn:aws:s3:::my-app-bucket/*"
      ]
    },
    {
      "Effect": "Allow",
      "Action": [
        "secretsmanager:GetSecretValue"
      ],
      "Resource": [
        "arn:aws:secretsmanager:us-east-1:123456789012:secret:my-db-credentials-*"
      ]
    }
  ]
}
```
### 2. IAM Role for Cluster Autoscaler

#### a) Trust Policy
```json
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Principal": {
      "Federated": "arn:aws:iam::123456789012:oidc-provider/oidc.eks.us-east-1.amazonaws.com/id/EXAMPLED539D4633E53DE1B716D3041E"
    },
    "Action": "sts:AssumeRoleWithWebIdentity",
    "Condition": {
      "StringEquals": {
        "oidc.eks.us-east-1.amazonaws.com/id/EXAMPLED539D4633E53DE1B716D3041E:sub": "system:serviceaccount:kube-system:cluster-autoscaler"
      }
    }
  }]
}
```
#### b) Permissions Policy
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "autoscaling:DescribeAutoScalingGroups",
        "autoscaling:DescribeAutoScalingInstances",
        "autoscaling:DescribeLaunchConfigurations",
        "autoscaling:DescribeTags",
        "autoscaling:SetDesiredCapacity",
        "autoscaling:TerminateInstanceInAutoScalingGroup"
      ],
      "Resource": "*"
    },
    {
      "Effect": "Allow",
      "Action": [
        "ec2:DescribeInstanceTypes",
        "ec2:DescribeLaunchTemplateVersions",
        "ec2:DescribeAvailabilityZones",
        "ec2:CreateFleet",
        "ec2:RunInstances",
        "ec2:TerminateInstances"
      ],
      "Resource": "*"
    }
  ]
}
```

### Usage

1. **Replace 123456789012 and the OIDC provider ARN with your AWS Account ID and EKS OIDC provider ARN.**

2. **Apply the trust policy and attach the permissions policy when creating each IAM role in AWS.**

3. **Annotate the corresponding Kubernetes ServiceAccounts with the new role ARNs.**

## Notes

- Ensure you have the necessary IAM roles and policies set up for IRSA and the Cluster Autoscaler.
- Customize the `k8s_orchestration.yaml` as needed for your application.

## Contributing

We ❤️ contributions! Please take a look at our [CONTRIBUTING.md](.github/CONTRIBUTING.md) for details on:

- Opening issues  
- Branching & commit message conventions  
- How to run tests locally  
- Our code review process  

By participating, you agree to abide by our [Code of Conduct](CODE_OF_CONDUCT.md).

## License
This project is licensed under the terms of the MIT license. See [LICENSE](LICENSE) for more information.