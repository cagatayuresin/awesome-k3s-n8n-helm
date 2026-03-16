# Awesome K3s n8n Helm

[![Awesome](https://awesome.re/badge.svg)](https://awesome.re)
[![Helm](https://img.shields.io/badge/Helm-v3-blue?logo=helm)](https://helm.sh)
[![Kubernetes](https://img.shields.io/badge/Kubernetes-K3s-326CE5?logo=kubernetes)](https://k3s.io)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![n8n](https://img.shields.io/badge/n8n-1.4.1-FF6C37?logo=n8n)](https://n8n.io)

A production-ready n8n Helm chart optimized for K3s with automatic Let's Encrypt SSL support via cert-manager.

## ✨ Features

- **Automatic SSL** - Let's Encrypt certificates via cert-manager
- **Persistent Storage** - Data survives pod restarts using local-path
- **Fully Configurable** - Domain, secrets, and image tags via values.yaml
- **Production Ready** - Resource limits, health checks, and security contexts
- **K3s Optimized** - Uses Traefik ingress and local-path storage class
- **Single Command Deploy** - Get n8n running in minutes

## 📋 Prerequisites

1. **K3s cluster** with Traefik ingress controller (included by default)
2. **cert-manager** installed for SSL certificates:

   ```bash
   kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.14.4/cert-manager.yaml
   ```

3. **Helm 3.x** installed
4. **DNS configured** - Your domain's A record pointing to your k3s node's public IP

## 🚀 Quick Start

### 1. Clone the repository

```bash
git clone https://github.com/cagatayuresin/awesome-k3s-n8n-helm.git
cd awesome-k3s-n8n-helm
```

### 2. Create your custom values file

```bash
cp n8n-helm/values.yaml my-values.yaml
```

### 3. Edit your values

```yaml
# my-values.yaml
domain: n8n.yourdomain.com

letsencrypt:
  email: your-email@yourdomain.com

postgres:
  password: "your-secure-postgres-password"

n8n:
  image:
    tag: 1.4.1
```

### 4. Install the chart

```bash
helm install my-n8n n8n-helm/ -f my-values.yaml
```

## ⚙️ Configuration

### Essential Parameters

| Parameter | Description | Default |
|-----------|-------------|---------|
| `domain` | Your domain name | `example.com` |
| `letsencrypt.email` | Email for Let's Encrypt | `admin@example.com` |
| `n8n.image.tag` | n8n image tag | `1.4.1` |
| `postgres.password` | PostgreSQL DB password | `changeme-postgres-password` |

### Full Configuration Reference

<details>
<summary>Click to expand all parameters</summary>

#### Namespace

| Parameter | Description | Default |
|-----------|-------------|---------|
| `namespace` | Kubernetes namespace | `n8n` |
| `createNamespace` | Create namespace | `true` |

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
| `n8n.image.tag` | Image tag | `1.4.1` |
| `n8n.replicas` | Number of replicas | `1` |
| `n8n.resources.requests.memory` | Memory request | `512Mi` |
| `n8n.resources.requests.cpu` | CPU request | `250m` |
| `n8n.resources.limits.memory` | Memory limit | `1Gi` |
| `n8n.resources.limits.cpu` | CPU limit | `1000m` |

#### PostgreSQL

| Parameter | Description | Default |
|-----------|-------------|---------|
| `postgres.image.repository` | Image repository | `postgres` |
| `postgres.image.tag` | Image tag | `16` |
| `postgres.database` | Database name | `n8n` |
| `postgres.user` | PostgreSQL user | `n8n` |
| `postgres.password` | PostgreSQL password | `changeme-postgres-password` |

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
helm install my-n8n n8n-helm/ -f my-values.yaml
```

### Upgrade

```bash
helm upgrade my-n8n n8n-helm/ -f my-values.yaml
```

### Uninstall

```bash
helm uninstall my-n8n
```

> ⚠️ **Note**: PersistentVolumeClaims are not deleted automatically. To delete all data:
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
│                     (n8nio/n8n:1.4.1)                           │
│                                                                 │
│  ┌─────────────────┐        ┌─────────────────────────────────┐ │
│  │     n8n Pod     │───────▶│  PVC: n8n-data (2Gi)            │ │
│  └────────┬────────┘        └─────────────────────────────────┘ │
└───────────┼─────────────────────────────────────────────────────┘
            │
            ▼
┌─────────────────────────────────────────────────────────────────┐
│                    PostgreSQL StatefulSet                       │
│                        (postgres:16)                            │
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
