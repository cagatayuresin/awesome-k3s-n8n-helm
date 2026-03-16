# Contributing to Awesome K3s n8n Helm

First off, thank you for considering contributing to this project! 🎉

## How Can I Contribute?

### Reporting Bugs

Before creating bug reports, please check existing issues. When creating a bug report, include:

- **Clear title** describing the issue
- **Steps to reproduce** the behavior
- **Expected behavior** vs actual behavior
- **Environment details** (K3s version, Helm version, etc.)
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
4. **Test** your changes
   ```bash
   helm lint n8n-helm/
   helm template test n8n-helm/
   ```
5. **Commit** your changes (`git commit -m 'Add amazing feature'`)
6. **Push** to the branch (`git push origin feature/amazing-feature`)
7. **Open a Pull Request**

## Development Setup

### Prerequisites

- Helm 3.x
- kubectl
- Access to a K3s cluster (optional for testing)

### Testing

```bash
# Lint the chart
helm lint n8n-helm/

# Render templates
helm template test n8n-helm/

# Dry-run installation
helm install test n8n-helm/ --dry-run --debug
```

## Code Style

- Use meaningful commit messages
- Keep YAML files properly indented (2 spaces)
- Comment complex template logic
- Update documentation for any changes

## Questions?

Feel free to open an issue for any questions!

---

Thank you for contributing! 🙏
