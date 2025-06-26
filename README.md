# Canine Helm Chart

This Helm chart deploys the Canine application with PostgreSQL and Redis dependencies on a Kubernetes cluster.

## Prerequisites

- Kubernetes 1.19+
- Helm 3.2.0+
- PV provisioner support in the underlying infrastructure (for PostgreSQL persistence)

## Installing the Chart

To install the chart with the release name `canine`:

```bash
helm install canine ./canine-chart
```

The command deploys Canine on the Kubernetes cluster in the default configuration. The [Parameters](#parameters) section lists the parameters that can be configured during installation.

## Uninstalling the Chart

To uninstall/delete the `canine` deployment:

```bash
helm delete canine
```

The command removes all the Kubernetes components associated with the chart and deletes the release.

## Parameters

### Global parameters

| Name                      | Description                                     | Value   |
| ------------------------- | ----------------------------------------------- | ------- |
| `nameOverride`            | String to partially override canine.fullname   | `""`    |
| `fullnameOverride`        | String to fully override canine.fullname       | `""`    |
| `replicaCount`            | Number of Canine replicas to deploy            | `1`     |

### Image parameters

| Name                | Description                          | Value                        |
| ------------------- | ------------------------------------ | ---------------------------- |
| `image.repository`  | Canine image repository              | `ghcr.io/czhu12/canine`      |
| `image.tag`         | Canine image tag                     | `"latest"`                   |
| `image.pullPolicy`  | Canine image pull policy             | `IfNotPresent`               |

### Application parameters

| Name                | Description                          | Value                        |
| ------------------- | ------------------------------------ | ---------------------------- |
| `app.port`          | Application port                     | `3000`                       |
| `app.auxPort`       | Auxiliary port                       | `3200`                       |
| `app.host`          | Application host URL                 | `"http://localhost:3000"`    |
| `app.localMode`     | Enable local mode                    | `true`                       |
| `app.username`      | Application username                 | `"canine"`                   |
| `app.password`      | Application password                 | `"test"`                     |

### Service parameters

| Name                    | Description                          | Value       |
| ----------------------- | ------------------------------------ | ----------- |
| `service.type`          | Kubernetes service type              | `NodePort`  |
| `service.port`          | Service port                         | `3000`      |
| `service.auxPort`       | Service auxiliary port               | `3200`      |
| `service.nodePort`      | NodePort for main service            | `30001`     |
| `service.auxNodePort`   | NodePort for auxiliary service       | `30002`     |

### PostgreSQL parameters

| Name                                    | Description                          | Value           |
| --------------------------------------- | ------------------------------------ | --------------- |
| `postgresql.enabled`                    | Enable PostgreSQL                    | `true`          |
| `postgresql.image.repository`           | PostgreSQL image repository          | `postgres`      |
| `postgresql.image.tag`                  | PostgreSQL image tag                 | `"16"`          |
| `postgresql.auth.username`              | PostgreSQL username                  | `"postgres"`    |
| `postgresql.auth.password`              | PostgreSQL password                  | `"password"`    |
| `postgresql.auth.databases`             | PostgreSQL databases to create       | `"canine_production,canine_development"` |
| `postgresql.persistence.enabled`        | Enable PostgreSQL persistence        | `true`          |
| `postgresql.persistence.storageClass`   | PostgreSQL storage class             | `"local-path"`  |
| `postgresql.persistence.size`           | PostgreSQL storage size              | `1Gi`           |

### Redis parameters

| Name                        | Description                          | Value       |
| --------------------------- | ------------------------------------ | ----------- |
| `redis.enabled`             | Enable Redis                         | `true`      |
| `redis.image.repository`    | Redis image repository               | `redis`     |
| `redis.image.tag`           | Redis image tag                      | `alpine`    |

### Volume parameters

| Name                              | Description                          | Value                    |
| --------------------------------- | ------------------------------------ | ------------------------ |
| `volumes.dockerSocket.enabled`    | Enable Docker socket volume         | `true`                   |
| `volumes.dockerSocket.hostPath`   | Docker socket host path             | `"/var/run/docker.sock"` |

### Namespace parameters

| Name                | Description                          | Value       |
| ------------------- | ------------------------------------ | ----------- |
| `namespace.create`  | Create namespace                     | `true`      |
| `namespace.name`    | Namespace name                       | `"canine"`  |

## Configuration and installation details

### Accessing the application

After installation, you can access the Canine application at:
- Main application: `http://<node-ip>:30001`
- Auxiliary service: `http://<node-ip>:30002`

### Customizing the installation

You can customize the installation by creating a custom values file:

```bash
# Create custom values
cat > my-values.yaml << EOF
app:
  username: "admin"
  password: "secure-password"
  host: "http://my-canine-app.com"

postgresql:
  auth:
    password: "secure-db-password"

service:
  type: LoadBalancer
EOF

# Install with custom values
helm install canine ./canine-chart -f my-values.yaml
```

### Upgrading the chart

To upgrade the chart:

```bash
helm upgrade canine ./canine-chart
```

## Troubleshooting

### Check pod status
```bash
kubectl get pods -n canine
```

### View logs
```bash
kubectl logs -n canine deployment/canine-web
kubectl logs -n canine statefulset/canine-postgres
kubectl logs -n canine deployment/canine-redis
```

### Check services
```bash
kubectl get services -n canine
