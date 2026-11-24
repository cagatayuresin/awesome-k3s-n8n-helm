# Contributing to Awesome K3s n8n Helm

First off, thank you for considering contributing to this project! 🎉

## How Can I Contribute?

### Reporting Bugs

Before creating bug reports, please check existing issues. When creating a bug report, include:

- **Clear title** describing the issue
- **Steps to reproduce** the behavior
- **Expected behavior** vs actual behavior
- **Environment details** (K3s version, Helm version, cert-manager version, etc.)
- **Logs** if applicable

### Suggesting Features

Feature requests are welcome! Please include:

- **Clear description** of the feature
- **Use case** - why is this feature needed?
- **Possible implementation** if you have ideas

### Pull Requests

1. **Fork** the repository
2. **Create a branch** for your feature (`git checkout -b feature/amazing-feature`)
3. **Make your changes**
4. **Test** your changes locally:
   ```bash
   helm lint n8n-helm/ --strict
   helm template test n8n-helm/
   ```
5. **Commit** your changes (`git commit -m 'feat: add amazing feature'`)
6. **Push** to the branch (`git push origin feature/amazing-feature`)
7. **Open a Pull Request** (CI will run YAML lint, Helm strict lint, `kubeconform` schema validation, Trivy security scan, and Kind E2E install/test)

## Development Setup

### Prerequisites

- Helm 3.8+ or Helm 4.x
- kubectl
- Access to a K3s or Kind cluster (optional for local E2E testing)

### Testing

```bash
# Strict lint the chart
helm lint n8n-helm/ --strict

# Render templates
helm template test n8n-helm/

# Dry-run installation
helm install test n8n-helm/ --dry-run --debug

# Run chart connection test after installing on a cluster
helm test test --logs
```

## Code Style

- Use Conventional Commits (`feat:`, `fix:`, `chore:`, `ci:`, `docs:`)
- Keep YAML files properly indented (2 spaces, validated by `.yamllint.yml`)
- Bump `version` in `n8n-helm/Chart.yaml` and update `CHANGELOG.md` for chart releases
- Update documentation for any value or template changes

## Questions?

Feel free to open an issue for any questions!

---

Thank you for contributing! 🙏
