# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

This is a home lab Kubernetes cluster managed via GitOps with FluxCD. The cluster runs on 3 CentOS 10 nodes (1 control plane, 2 workers) and hosts various microservices for learning and experimentation with cloud-native technologies, observability, and data collection.

## Repository Structure

- `cluster/` - Main GitOps directory monitored by Flux
  - `flux-system/` - Flux configuration and sync manifests
  - `apps/` - Application deployments (microservices, collectors)
  - `infrastructure/` - Infrastructure components (databases, monitoring, networking)
- `docs/` - Setup and troubleshooting documentation
- `archived/` - Old configurations kept for reference

## GitOps with FluxCD

This cluster uses **FluxCD** for GitOps continuous delivery. Changes pushed to the `main` branch are automatically reconciled by Flux.

### Flux Commands

```bash
# View Flux logs
flux logs

# Check kustomization status
flux get kustomizations

# Filter logs by keyword
flux logs --all-namespaces | grep <keyword>

# Force reconciliation
flux reconcile kustomization flux-system
```

### Deployment Pattern

Each application has two kustomization files:
- `kustomization.yaml` - Standard Kustomize file listing Kubernetes resources
- `kustomization-flux.yaml` - FluxCD Kustomization resource that tells Flux how to deploy

The root Flux sync watches `./cluster` and automatically deploys everything within.

## Kubernetes Cluster Details

**Cluster Network:**
- Control plane: `k8s-controller` (192.168.86.200)
- Worker 1: `k8s-worker1` (192.168.86.201)
- Worker 2: `k8s-worker2` (192.168.86.202)
- Pod network CIDR: `10.244.0.0/16` (Flannel)
- MetalLB IP pool: `192.168.86.220-192.168.86.240`

**Container Runtime:** containerd with systemd cgroup driver

**Storage:** NFS client provisioner connected to Synology NAS at `192.168.86.210:/volume2/k8s-data`

## Infrastructure Components

The following infrastructure is deployed via Helm or manifests in `cluster/infrastructure/`:

**Databases:**
- PostgreSQL (multiple instances: homelab, backstage, homelab-hub)
- MongoDB with Mongo Express UI
- Redis
- Elasticsearch (with Kibana, APM Server, Filebeat, Logstash)
- Splunk

**Messaging:**
- Kafka with Kafka UI

**Monitoring & Observability:**
- Prometheus
- Grafana
- Tempo (tracing)
- OpenTelemetry Operator and Collector
- Kubernetes Dashboard

**Networking:**
- MetalLB (load balancer)
- cert-manager

## Application Architecture

Applications are Python-based microservices with OpenTelemetry instrumentation:

**APIs:**
- `api.authentication` - Authentication service
- `api.login` - Login service
- `api.member` - Member management service

**Data Collectors:**
- `weather-collector` - Weather data collection
- `enphase-collector` - Enphase solar data
- `emporia-collector` - Emporia energy monitoring data
- `synology-collector` - Synology NAS metrics
- `network-collector` - Network metrics

**Other:**
- `homelab-hub` - Central hub application
- `dice-roll` - Demo/test application
- `dart-scorer` - Dart game scoring application

### Common Deployment Patterns

All applications follow similar patterns:
- Namespace per app or logical grouping
- Deployment with OpenTelemetry environment variables
- Service for internal cluster communication
- Secrets for credentials (PostgreSQL, MongoDB, Kafka)
- Images hosted on GitHub Container Registry (ghcr.io/jaysuzi5/*)

### OpenTelemetry Configuration

Standard OTEL environment variables used across applications:
```yaml
- name: OTEL_EXPORTER_OTLP_ENDPOINT
  value: "http://otel-collector-elastic.elasticsearch.svc.cluster.local:4318"
- name: OTEL_EXPORTER_OTLP_PROTOCOL
  value: "http/protobuf"
- name: OTEL_LOGS_EXPORTER
  value: "otlp"
- name: OTEL_SERVICE_NAME
  value: "<service-name>"
- name: OTEL_PYTHON_LOGGING_AUTO_INSTRUMENTATION_ENABLED
  value: "true"
```

## Common Kubernetes Operations

```bash
# View API server logs
kubectl logs -n kube-system -l component=kube-apiserver

# Check nodes
kubectl get nodes

# Watch pods across all namespaces
kubectl get pods -A -o wide

# Port forward to Prometheus
kubectl port-forward -n monitoring svc/prometheus-operator-kube-p-prometheus 9090
# Access at http://localhost:9090/targets

# Port forward to other services (similar pattern)
kubectl port-forward -n <namespace> svc/<service-name> <local-port>:<service-port>
```

## Adding a New Node

When adding a new worker node:

1. Prepare the system (disable swap, SELinux, configure kernel modules)
2. Install containerd runtime
3. Install kubeadm, kubelet, kubectl (v1.30)
4. Install nfs-utils: `sudo yum install -y nfs-utils`
5. Get join command: `kubeadm token create --print-join-command`
6. Run join command on new node
7. Verify with `kubectl get nodes`
8. Label node: `kubectl label node <node-name> node-role.kubernetes.io/worker=worker`

See [docs/01_initial_setup.md](docs/01_initial_setup.md) for detailed steps.

## Secrets Management

Secrets are stored as Kubernetes Secret manifests in the repository. Common secrets:
- `postgresql-homelab-secret` - PostgreSQL credentials (POSTGRES_USER, POSTGRES_PASSWORD)
- `mongodb-credentials` - MongoDB credentials (username, password)
- Various collector-specific secrets (weather API keys, etc.)

**Note:** Secret values in the repository should be base64 encoded. Never commit plain text secrets.

## SSH Access

From development machine, SSH shortcuts are configured:
```bash
ssh controller  # k8s-controller
ssh worker1     # k8s-worker1
ssh worker2     # k8s-worker2
```

## Common Issues

**MetalLB:** If external IPs aren't assigned, verify the IP pool doesn't overlap with the node network. MetalLB uses 192.168.86.220-240, nodes use 192.168.86.200-202.

**NFS Storage:** All worker nodes need nfs-utils installed for persistent volumes to mount correctly.

**Flux Reconciliation:** If changes aren't applying, check Flux logs and force reconciliation. Ensure the resource is in the correct path and included in a kustomization.yaml.

See [docs/99_troubleshooting.md](docs/99_troubleshooting.md) for upgrade procedures and additional troubleshooting.

## Development Workflow

1. Make changes to manifests in `cluster/apps/` or `cluster/infrastructure/`
2. Commit and push to `main` branch
3. Flux automatically detects and applies changes (1 minute interval)
4. Monitor with `flux logs` or `kubectl get pods -A -w`

For application code changes:
1. Build and push container image to ghcr.io/jaysuzi5/
2. Update image tag in deployment.yaml
3. Commit and push - Flux will roll out the new version
