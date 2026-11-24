# Awesome K3s n8n Helm

[![Awesome](https://awesome.re/badge.svg)](https://awesome.re)
[![CI](https://github.com/cagatayuresin/awesome-k3s-n8n-helm/actions/workflows/ci.yml/badge.svg)](https://github.com/cagatayuresin/awesome-k3s-n8n-helm/actions/workflows/ci.yml)
[![Helm](https://img.shields.io/badge/Helm-v3%20%7C%20v4-blue?logo=helm)](https://helm.sh)
[![Kubernetes](https://img.shields.io/badge/Kubernetes-K3s-326CE5?logo=kubernetes)](https://k3s.io)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![n8n](https://img.shields.io/badge/n8n-2.49.5-FF6C37?logo=n8n)](https://n8n.io)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-18-4169E1?logo=postgresql&logoColor=white)](https://www.postgresql.org)

A production-ready n8n Helm chart optimized for K3s with automatic Let's Encrypt SSL support via cert-manager and OCI distribution via GitHub Container Registry (GHCR).

## ✨ Features

- **OCI Registry Ready** - Install directly from GitHub Container Registry (`oci://ghcr.io/cagatayuresin/charts/n8n`)
- **Automatic SSL** - Let's Encrypt certificates via cert-manager (`ingressClassName` solver)
- **Persistent Storage** - Data survives pod restarts using K3s `local-path` with PostgreSQL 18 volume layout
- **Fully Configurable** - Domain, secrets, encryption key, proxy hops, and image tags via `values.yaml` (with `values.schema.json` validation)
- **Production Ready** - Resource limits, `/healthz` & `/healthz/readiness` probes, security contexts, and `Recreate` strategy for RWO PVCs
- **K3s Optimized** - Uses Traefik ingress and `local-path` storage class
- **Single Command Deploy** - Get n8n running in minutes

## 📋 Prerequisites

1. **K3s cluster** with Traefik ingress controller (included by default)
2. **cert-manager** installed for SSL certificates:

   ```bash
   kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.21.3/cert-manager.yaml
   ```

3. **Helm 3.8+ or Helm 4.x** installed (required for OCI registry support)
4. **DNS configured** - Your domain's A record pointing to your K3s node's public IP

## 🚀 Quick Start

### Option A: Install directly from GitHub Container Registry (Recommended)

#### 1. Create your custom values file

```yaml
# my-values.yaml
domain: n8n.yourdomain.com

letsencrypt:
  email: your-email@yourdomain.com

postgres:
  password: "your-secure-postgres-password"

n8n:
  encryptionKey: "your-long-random-encryption-key"
  image:
    tag: "2.49.5"
```

#### 2. Install the chart from GHCR

```bash
helm install my-n8n oci://ghcr.io/cagatayuresin/charts/n8n \
  --version 2.0.0 \
  -f my-values.yaml
```

### Option B: Install from source

```bash
git clone https://github.com/cagatayuresin/awesome-k3s-n8n-helm.git
cd awesome-k3s-n8n-helm
cp n8n-helm/values.yaml my-values.yaml
# Edit my-values.yaml with your domain, email, postgres.password, and n8n.encryptionKey
helm install my-n8n n8n-helm/ -f my-values.yaml
```

> 💡 **Note on namespaces**: By default, `createNamespace: true` creates the `n8n` namespace with `helm.sh/resource-policy: keep` (so uninstalling will not wipe your PVCs). If you prefer passing `--namespace <ns> --create-namespace` to Helm directly, set `createNamespace: false` in your values file.

## ⚙️ Configuration

### Essential Parameters

| Parameter | Description | Default |
|-----------|-------------|---------|
| `domain` | Your domain name | `example.com` |
| `letsencrypt.email` | Email for Let's Encrypt | `admin@example.com` |
| `n8n.image.tag` | n8n image tag | `"2.49.5"` |
| `n8n.encryptionKey` | Encryption key for n8n credentials (recommended) | `""` |
| `postgres.password` | PostgreSQL DB password | `changeme-postgres-password` |

### Full Configuration Reference

<details>
<summary>Click to expand all parameters</summary>

#### Namespace

| Parameter | Description | Default |
|-----------|-------------|---------|
| `namespace` | Kubernetes namespace (falls back to release namespace if empty) | `n8n` |
| `createNamespace` | Create namespace resource with `keep` policy | `true` |

#### Domain & SSL

| Parameter | Description | Default |
|-----------|-------------|---------|
| `domain` | Your domain | `example.com` |
| `enableWwwRedirect` | Handle www subdomain | `true` |
| `letsencrypt.email` | Certificate email | `admin@example.com` |
| `letsencrypt.server` | ACME server URL | production |
| `letsencrypt.createClusterIssuer` | Create ClusterIssuer | `true` |
| `letsencrypt.clusterIssuerName` | ClusterIssuer name | `letsencrypt-prod` |

#### n8n

| Parameter | Description | Default |
|-----------|-------------|---------|
| `n8n.image.repository` | Image repository | `docker.n8n.io/n8nio/n8n` |
| `n8n.image.tag` | Image tag | `"2.49.5"` |
| `n8n.replicas` | Number of replicas | `1` |
| `n8n.strategy` | Deployment strategy (`Recreate` or `RollingUpdate`) | `Recreate` |
| `n8n.encryptionKey` | Secret key for encrypting stored credentials | `""` |
| `n8n.proxyHops` | Number of reverse proxies in front of n8n (`N8N_PROXY_HOPS`) | `1` |
| `n8n.timezone` | Timezone (`GENERIC_TIMEZONE`) | `Europe/Istanbul` |
| `n8n.resources.requests.memory` | Memory request | `512Mi` |
| `n8n.resources.requests.cpu` | CPU request | `250m` |
| `n8n.resources.limits.memory` | Memory limit | `1Gi` |
| `n8n.resources.limits.cpu` | CPU limit | `1000m` |
| `n8n.extraEnv` | Additional environment variables | `[]` |

#### PostgreSQL

| Parameter | Description | Default |
|-----------|-------------|---------|
| `postgres.image.repository` | Image repository | `postgres` |
| `postgres.image.tag` | Image tag | `"18"` |
| `postgres.initImage.repository` | Init container image for volume ownership | `busybox` |
| `postgres.initImage.tag` | Init container image tag | `"1.37"` |
| `postgres.database` | Database name | `n8n` |
| `postgres.user` | PostgreSQL user | `n8n` |
| `postgres.password` | PostgreSQL password | `changeme-postgres-password` |
| `postgres.extraEnv` | Additional environment variables | `[]` |

#### Health Checks

| Parameter | Description | Default |
|-----------|-------------|---------|
| `healthChecks.n8n.enabled` | Enable n8n probes | `true` |
| `healthChecks.n8n.livenessPath` | n8n liveness probe endpoint | `/healthz` |
| `healthChecks.n8n.readinessPath` | n8n readiness probe endpoint (checks DB & migrations) | `/healthz/readiness` |
| `healthChecks.postgres.enabled` | Enable PostgreSQL `pg_isready` probes | `true` |

#### Persistence

| Parameter | Description | Default |
|-----------|-------------|---------|
| `persistence.storageClass` | Storage class | `local-path` |
| `persistence.n8n.enabled` | Enable n8n persistence | `true` |
| `persistence.n8n.size` | n8n PVC size | `2Gi` |
| `persistence.postgres.enabled` | Enable Postgres persistence | `true` |
| `persistence.postgres.size` | Postgres PVC size | `5Gi` |

</details>

## 📖 Usage

### Install

```bash
helm install my-n8n oci://ghcr.io/cagatayuresin/charts/n8n --version 2.0.0 -f my-values.yaml
```

### Verify Deployment

```bash
helm test my-n8n --logs
```

### Upgrade

```bash
helm upgrade my-n8n oci://ghcr.io/cagatayuresin/charts/n8n --version 2.0.0 -f my-values.yaml
```

> ⚠️ **Upgrading from Chart `1.x` (PostgreSQL 16) to `2.x` (PostgreSQL 18)**:
> PostgreSQL major versions are not binary-compatible on disk, and PostgreSQL 18 changed the Docker volume mount layout to `/var/lib/postgresql`. Back up your database with `pg_dump`, recreate the PostgreSQL PVC, and restore with `pg_restore` (or pin `postgres.image.tag: "16"` if you want to defer the DB upgrade).

### Uninstall

```bash
helm uninstall my-n8n
```

> ⚠️ **Note**: PersistentVolumeClaims (and the namespace) are not deleted automatically. To delete all data:
>
> ```bash
> kubectl delete pvc -l app.kubernetes.io/instance=my-n8n -n n8n
> ```

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                             Internet                            │
└─────────────────────────────────┬───────────────────────────────┘
                                  │
                                  ▼
┌─────────────────────────────────────────────────────────────────┐
│                    Traefik Ingress (K3s)                        │
│                     + Let's Encrypt TLS                         │
└─────────────────────────────────┬───────────────────────────────┘
                                  │
                                  ▼
┌─────────────────────────────────────────────────────────────────┐
│                        n8n Deployment                           │
│                   (n8nio/n8n:2.49.5)                            │
│                                                                 │
│  ┌─────────────────┐        ┌─────────────────────────────────┐ │
│  │     n8n Pod     │───────▶│  PVC: n8n-data (2Gi)            │ │
│  └────────┬────────┘        └─────────────────────────────────┘ │
└───────────┼─────────────────────────────────────────────────────┘
            │
            ▼
┌─────────────────────────────────────────────────────────────────┐
│                    PostgreSQL StatefulSet                       │
│                        (postgres:18)                            │
│                                                                 │
│  ┌─────────────────┐        ┌─────────────────────────────────┐ │
│  │  Postgres Pod   │───────▶│  PVC: postgres-data (5Gi)       │ │
│  └─────────────────┘        └─────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────────┘
```

## 🤝 Contributing

Contributions are welcome! Please read our [Contributing Guide](CONTRIBUTING.md) for details.

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## ⭐ Show Your Support

If this project helped you, please give it a ⭐ on GitHub!

## 📬 Contact

- GitHub: [@cagatayuresin](https://github.com/cagatayuresin)

---

Made with ❤️ for the K3s community
