# Contributing Guidelines

Thank you for considering contributing to the **EKS GitOps Deployment** project! Your help makes this project better. Please take a moment to read through these guidelines before contributing.

## 1. How to Contribute

### Reporting Issues
- Search existing [issues](https://github.com/your-org/your-repo/issues) to see if your problem or feature request has already been reported.
- If not, open a new issue with:
  - A clear and descriptive title.
  - A detailed description of the problem or the feature you’d like to see.
  - Relevant logs, error messages, or screenshots.

### Submitting Pull Requests
1. **Fork** the repository and create your branch from `main`:
   ```bash
   git checkout -b my-feature-branch main
   ```
2. **Make your changes**: follow existing code style and add tests if applicable.
3. **Run tests and linters** locally:
   ```bash
   # Example: validate Kubernetes manifests
   kubeval k8s_orchestration.yaml
   ```
4. **Commit** your changes with a clear message:
   ```bash
   git commit -m "feat: add awesome new feature"
   ```
5. **Push** your branch to your fork:
   ```bash
   git push origin my-feature-branch
   ```
6. **Open a Pull Request** against `main` in the original repo.

## 2. Code Style
- YAML manifests should be linted with `kubeval` or `yamllint`.
- Follow Kubernetes resource naming conventions (lowercase, hyphens).
- Use Kustomize overlays for environment-specific configuration.

## 3. Git Commit Conventions
We use [conventional commits](https://www.conventionalcommits.org/):
- **feat:** a new feature
- **fix:** a bug fix
- **docs:** documentation only changes
- **style:** formatting, missing semicolons, etc; no code change
- **refactor:** code change that neither fixes a bug nor adds a feature
- **test:** adding missing tests or correcting existing tests

Example:
```
feat: support custom namespace via CLI flag
```

## 4. Reviewing & Merging
- All PRs require at least one approving review from a maintainer.
- CI must pass (Actions build, image scan, etc.) before merging.
- PRs should be rebased or merged to resolve conflicts, not force-pushed.

## 5. Code of Conduct
By participating, you agree to abide by the project’s [Code of Conduct](CODE_OF_CONDUCT.md).

## 6. Getting Help
If you have questions, join the discussion in [issues](https://github.com/your-org/your-repo/issues) or contact the maintainers.

---

Thank you for helping improve **EKS GitOps Deployment**! We look forward to your contributions.  
