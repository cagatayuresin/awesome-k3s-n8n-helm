# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [2.0.0] - 2026-11-24

### Breaking Changes

- Upgraded default PostgreSQL image from `postgres:16` to `postgres:18` and aligned PVC mount path to `/var/lib/postgresql` per PostgreSQL 18 Docker standards. Existing `1.x` users must migrate PostgreSQL data via `pg_dump`/`pg_restore` or keep `postgres.image.tag: "16"`.
- Switched cert-manager `ClusterIssuer` HTTP-01 solver from deprecated `http01.ingress.class` to `http01.ingress.ingressClassName`.

### Changed

- Updated default n8n version from `2.11.4` to `2.49.5` (`appVersion: "2.49.5"`).
- Updated cert-manager prerequisite documentation from `v1.14.4` to `v1.21.3`.
- Updated n8n readiness probe to use `/healthz/readiness` (verifies database connectivity & migrations) while keeping `/healthz` for liveness.
- Replaced mutable `busybox:latest` in PostgreSQL init container with configurable pinned `busybox:1.37` and explicit resource requests/limits.
- Defaulted n8n Deployment strategy to `Recreate` to avoid ReadWriteOnce PVC lock conflicts on K3s `local-path`.
- Upgraded GitHub Actions workflows to latest versions (`actions/checkout@v7`, `azure/setup-helm@v4`, Helm `v4.3.2`).
- Removed CI-based SonarQube workflow and `sonar-project.properties` in favor of SonarCloud Automatic Analysis.

### Added

- `templates/namespace.yaml` implementing `createNamespace: true` (with `helm.sh/resource-policy: keep`).
- `values.schema.json` for built-in Helm values validation.
- `templates/NOTES.txt` with post-install instructions and security warnings.
- `templates/tests/test-connection.yaml` for `helm test` verification.
- Optional `n8n.encryptionKey` (`N8N_ENCRYPTION_KEY`) and `n8n.proxyHops` (`N8N_PROXY_HOPS`) support.
- `checksum/secret` pod annotations on n8n Deployment and PostgreSQL StatefulSet for automatic rollouts on secret changes.
- `nodeSelector`, `affinity`, `tolerations`, `imagePullSecrets`, and `extraEnv` support on PostgreSQL StatefulSet.
- Professional CI pipeline (`.github/workflows/ci.yml`) with `yamllint`, `helm lint --strict`, `kubeconform` Kubernetes + CRD schema validation, Trivy IaC security scanning, and Kind E2E `helm install` / `helm test` / `helm upgrade` / `helm uninstall`.
- Automated OCI Helm chart publishing to GitHub Container Registry (`oci://ghcr.io/cagatayuresin/charts/n8n`), build provenance attestation (`actions/attest@v4`), and GitHub Releases via `.github/workflows/release.yml`.
- Dependabot configuration (`.github/dependabot.yml`) for weekly GitHub Actions updates.

### Fixed

- Removed stale commented template expressions in `statefulset.yaml` that rendered `<no value>`.
- Guarded PostgreSQL init container with `persistence.postgres.enabled` so disabling persistence works cleanly.
- Eliminated stray empty YAML document separators (`---`) in `pvc.yaml` and `ingress.yaml` when optional resources are disabled.

### Security

- Hardened n8n container securityContext (`runAsNonRoot: true`, `allowPrivilegeEscalation: false`, `capabilities.drop: [ALL]`).
- Added Trivy IaC misconfiguration checks in CI.

## [1.0.0] - 2026-03-16

### Added

- Initial release of Awesome K3s n8n Helm chart
- n8n v2.11.4 support
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
