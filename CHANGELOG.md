# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.0.0] - 2026-03-16

### Added

- Initial release of Awesome K3s n8n Helm chart
- n8n v1.4.1 support
- PostgreSQL 16 StatefulSet with persistent storage
- Automatic Let's Encrypt SSL via cert-manager
- Traefik ingress integration for K3s
- Configurable values for:
  - Domain and www redirect
  - Let's Encrypt email and server
  - n8n and PostgreSQL image tags
  - Database credentials
  - Resource limits and requests
  - Persistence storage size
- Production-ready example values
- Comprehensive documentation

### Security

- Secrets stored as Kubernetes Secrets
- Security contexts for pods
- TLS termination at ingress level
