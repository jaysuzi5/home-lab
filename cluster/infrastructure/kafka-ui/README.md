# Kafka UI
A UI for managing Kafka

See more at:  https://github.com/provectus/kafka-ui


### GitOps Structure
The following are stored here, but the kustomization.yaml is blank as this will be maintained in a consistent directory structue, but 
deployed out side of flux as with a lot of the existing infrasture

```bash
.
├── namespace.yaml          # define and created manually
├── values.yaml             # will use with the helm command
└── kustomization.yaml      # defined as blank so no reconcilation will happen
```

### heml installation

```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update

helm install kafka bitnami/kafka -n kafka -f values.yaml
```

to update:
```bash
helm upgrade kafka bitnami/kafka -n kafka -f values.yaml
```

```

### Monitor installation and debug issues
Validate that the reconcilliation started:
```bash
flux logs
```

See details of kustomization logs
```bash
flux logs --kind=Kustomization --name=flux-system -n flux-system  --tail=10
```

See status of the reconcilliation:
```bash
kubectl get kustomization -n flux-system
```


### Validate Installation
Check the pods
```bash
kubectl get pods -n kafka-ui
```

Check the services
```bash
kubectl get srv -n kafka-ui
```

Use the defined IP to connect.  Add to local /etc/hosts if desired
