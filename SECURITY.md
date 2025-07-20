# Security Best Practices

This document outlines recommended security measures and best practices for the EKS GitOps deployment.

## 1. Identity & Access Management
- **IAM Roles for Service Accounts (IRSA):** Map each Kubernetes ServiceAccount to a dedicated IAM role to enforce least privilege.
- **RBAC Policies:** Define granular Roles and RoleBindings to limit user and pod permissions to only what’s necessary.
- **AWS IAM Authenticator:** Use IAM for `kubectl` authentication and restrict API access via the `aws-auth` ConfigMap.

## 2. Network Isolation & Traffic Control
- **VPC Architecture:** Place nodes in private subnets; expose only LoadBalancer/NLB in public subnets.
- **Security Groups:** Restrict node and load balancer traffic at the VPC level.
- **Network Policies:** Implement Kubernetes NetworkPolicies (e.g., via Calico) to restrict pod-to-pod communication.

## 3. Pod Hardening
- **Pod Security Admission:** Enable the `Restricted` PodSecurity level to block privileged containers, host mounts, and unrestricted capabilities.
- **SecurityContext:** Run containers as non-root and drop all unnecessary Linux capabilities.
- **Seccomp Profiles:** Use the default `RuntimeDefault` seccomp profile or a custom profile for syscall filtering.

## 4. Secrets Management
- **Encryption at Rest:** Enable KMS-backed encryption of Kubernetes Secret resources in etcd.
- **External Secret Store:** Use ExternalSecrets Operator with AWS Secrets Manager or HashiCorp Vault.
- **SealedSecrets:** For GitOps workflows, encrypt secrets at rest in Git using Bitnami SealedSecrets.

## 5. Cluster & Node Security
- **Private API Endpoint:** Restrict the EKS control plane endpoint to trusted CIDRs or use a private endpoint.
- **Managed Node Groups:** Use EKS-managed NodeGroups or Bottlerocket for minimal OS footprint and automated patching.
- **Node IAM Scoping:** Minimize EC2 instance profile permissions and delegate AWS access to pods via IRSA.

## 6. Supply Chain Security
- **Image Scanning:** Integrate Trivy or ECR scanning in CI to catch vulnerabilities before deployment.
- **Immutable Tags:** Deploy images by digest (SHA256) rather than `:latest` tag.
- **Image Signing:** Adopt Sigstore/cosign to sign and verify images in your pipeline.

## 7. Runtime Protection & Monitoring
- **Audit Logging:** Forward Kubernetes API audit logs and pod logs to CloudWatch or ELK for analysis.
- **Runtime Security:** Deploy Falco or AWS GuardDuty for detection of anomalous container behavior.
- **Network Monitoring:** Enable VPC Flow Logs and Cilium Hubble for network-level visibility.

## 8. Encryption In Transit
- **mTLS:** Consider a service mesh (Istio, App Mesh) for mutual TLS between services.
- **TLS Termination:** Use cert-manager and ACM/LetsEncrypt to issue certificates for Ingress and service endpoints.

## 9. Patching & Compliance
- **Regular Upgrades:** Keep the EKS control plane within one minor version of latest and rotate node groups frequently.
- **CIS Benchmark Audits:** Use `kube-bench` to audit cluster conformance and remediate findings.
- **Policy Enforcement:** Leverage OPA/Gatekeeper or Kyverno to enforce best-practice policies at admission.

---

For detailed manifests and configuration examples, refer to the `k8s_orchestration.yaml` and follow the guidelines in the repository.
