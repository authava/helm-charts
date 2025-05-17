# AuthGate Helm Chart

This Helm chart deploys AuthGate, a standalone Traefik forwardAuth middleware for authentication and authorization, along with its supporting components.

## Prerequisites

- Kubernetes 1.19+
- Helm 3.2.0+

## Getting Started

### Add the Helm Repository

```bash
helm repo add authgate https://authava.github.io/helm-charts
helm repo update
```

### Install the Chart

```bash
# Install with default values
helm install authgate authgate/authgate

# Install with custom values file
helm install authgate authgate/authgate -f values.yaml

# Install with specific values
helm install authgate authgate/authgate --set authgate.config.adminApi.token=mysecrettoken
```

## Components

The chart deploys the following components:

- **AuthGate**: The main authentication/authorization service
- **AuthGate Admin UI**: Admin interface for managing routes and configurations
- **PostgreSQL**: Database for storing configuration (optional, using Bitnami chart)
- **Redis**: Cache for session data (optional, using Bitnami chart)
- **Traefik**: Ingress controller (optional, using Bitnami chart)
- **Token Handoff**: Service for token handling

## Configuration

### Global Parameters

| Parameter | Description | Default |
|-----------|-------------|---------|
| `nameOverride` | Override the name of the chart | `""` |
| `fullnameOverride` | Override the full name of the chart | `""` |

### AuthGate Parameters

| Parameter | Description | Default |
|-----------|-------------|---------|
| `authgate.image.repository` | AuthGate image repository | `authava/authgate` |
| `authgate.image.tag` | AuthGate image tag | `latest` |
| `authgate.image.pullPolicy` | AuthGate image pull policy | `IfNotPresent` |
| `authgate.replicaCount` | Number of AuthGate replicas | `1` |
| `authgate.config.port` | Port to listen on | `4181` |
| `authgate.config.logLevel` | Logging level | `info` |
| `authgate.config.adminApi.enabled` | Enable the Admin API | `true` |
| `authgate.config.adminApi.token` | Bearer token for Admin API authentication | `""` |
| `authgate.config.adminApi.sessionRoles` | Roles allowed to access the Admin API | `admin,superuser` |
| `authgate.config.sessionCookie` | Name of the session cookie | `session` |
| `authgate.config.configBackend` | Configuration backend to use | `postgres` |
| `authgate.config.configPath` | Path to the configuration file | `/app/authgate.json` |
| `authgate.config.cache.enabled` | Enable session caching | `true` |
| `authgate.config.cache.backend` | Cache backend to use | `redis` |
| `authgate.customConfig.enabled` | Enable custom configuration | `false` |
| `authgate.customConfig.configMap` | ConfigMap containing custom configuration | `""` |
| `authgate.service.type` | Service type | `ClusterIP` |
| `authgate.service.port` | Service port | `4181` |

### AuthGate Admin UI Parameters

| Parameter | Description | Default |
|-----------|-------------|---------|
| `authgateAdminUi.enabled` | Enable AuthGate Admin UI | `true` |
| `authgateAdminUi.image.repository` | AuthGate Admin UI image repository | `authava/authgate-admin-ui` |
| `authgateAdminUi.image.tag` | AuthGate Admin UI image tag | `latest` |
| `authgateAdminUi.image.pullPolicy` | AuthGate Admin UI image pull policy | `IfNotPresent` |
| `authgateAdminUi.replicaCount` | Number of AuthGate Admin UI replicas | `1` |
| `authgateAdminUi.config.apiUrl` | API URL for the Admin UI | `https://admin.yourdomain.com/admin` |
| `authgateAdminUi.service.type` | Service type | `ClusterIP` |
| `authgateAdminUi.service.port` | Service port | `80` |

### Token Handoff Parameters

| Parameter | Description | Default |
|-----------|-------------|---------|
| `tokenHandoff.enabled` | Enable Token Handoff | `true` |
| `tokenHandoff.image.repository` | Token Handoff image repository | `authava/token-handoff` |
| `tokenHandoff.image.tag` | Token Handoff image tag | `latest` |
| `tokenHandoff.image.pullPolicy` | Token Handoff image pull policy | `IfNotPresent` |
| `tokenHandoff.replicaCount` | Number of Token Handoff replicas | `1` |
| `tokenHandoff.config.port` | Port to listen on | `3000` |
| `tokenHandoff.config.cookieName` | Name of the cookie | `session` |
| `tokenHandoff.config.cookieDomain` | Domain for the cookie | `yourdomain.com` |
| `tokenHandoff.config.cookiePath` | Path for the cookie | `/` |
| `tokenHandoff.config.defaultRedirect` | Default redirect URL | `https://admin.yourdomain.com` |
| `tokenHandoff.config.defaultToken` | Default token | `""` |
| `tokenHandoff.config.secureCookie` | Enable secure cookie | `true` |
| `tokenHandoff.config.httpOnly` | Enable HTTP-only cookie | `true` |
| `tokenHandoff.service.type` | Service type | `ClusterIP` |
| `tokenHandoff.service.port` | Service port | `3000` |

### PostgreSQL Parameters

| Parameter | Description | Default |
|-----------|-------------|---------|
| `postgresql.enabled` | Enable PostgreSQL | `true` |
| `postgresql.auth.username` | PostgreSQL username | `authgate` |
| `postgresql.auth.password` | PostgreSQL password | `""` |
| `postgresql.auth.database` | PostgreSQL database | `authgate` |
| `postgresql.primary.persistence.enabled` | Enable persistence | `true` |
| `postgresql.primary.persistence.size` | Persistence size | `8Gi` |

### Redis Parameters

| Parameter | Description | Default |
|-----------|-------------|---------|
| `redis.enabled` | Enable Redis | `true` |
| `redis.architecture` | Redis architecture | `standalone` |
| `redis.auth.enabled` | Enable Redis authentication | `true` |
| `redis.auth.password` | Redis password | `""` |
| `redis.master.persistence.enabled` | Enable persistence | `true` |
| `redis.master.persistence.size` | Persistence size | `8Gi` |

### Traefik Parameters

| Parameter | Description | Default |
|-----------|-------------|---------|
| `traefik.enabled` | Enable Traefik | `true` |
| `traefik.additionalArguments` | Additional Traefik arguments | See values.yaml |
| `traefik.persistence.enabled` | Enable persistence | `true` |
| `traefik.persistence.path` | Persistence path | `/data` |
| `traefik.persistence.size` | Persistence size | `128Mi` |
| `traefik.service.type` | Service type | `LoadBalancer` |

### Ingress Parameters

| Parameter | Description | Default |
|-----------|-------------|---------|
| `ingress.enabled` | Enable ingress | `true` |
| `ingress.annotations` | Ingress annotations | See values.yaml |
| `ingress.hosts` | Ingress hosts | See values.yaml |
| `ingress.tls` | Ingress TLS configuration | See values.yaml |

## Examples

### Minimal Installation (In-Memory Cache, No Database)

```yaml
# values.yaml
authgate:
  config:
    configBackend: "json"
    cache:
      backend: "memory"

postgresql:
  enabled: false

redis:
  enabled: false
```

### Production Setup

```yaml
# values.yaml
authgate:
  replicaCount: 3
  config:
    adminApi:
      token: "your-secure-token"
    cache:
      enabled: true
      backend: "redis"

authgateAdminUi:
  replicaCount: 2
  config:
    apiUrl: "https://admin.yourdomain.com/admin"

postgresql:
  auth:
    password: "your-secure-password"
  primary:
    persistence:
      size: 20Gi
      storageClass: "managed-premium"

redis:
  architecture: replication
  auth:
    password: "your-secure-password"
  master:
    persistence:
      size: 10Gi
      storageClass: "managed-premium"

ingress:
  hosts:
    - host: admin.yourdomain.com
      paths:
        - path: /
          pathType: Prefix
  tls:
    - secretName: admin-tls
      hosts:
        - admin.yourdomain.com
```

## Upgrading

### To 1.0.0

This is the first stable release of the AuthGate Helm chart.

## License

MIT