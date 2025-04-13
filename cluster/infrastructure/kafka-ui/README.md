# Kafka UI
A UI for managing Kafka

See more at:  https://github.com/provectus/kafka-ui


### GitOps Structure
Using a GitOps installation via Flux/Kustomization.  Using a sealed secret for the UI user with basic authentication.

```bash
.
├── namespace.yaml          # define and created manually
├── namespace.yaml          # define and created manually
├── namespace.yaml          # define and created manually
├── namespace.yaml          # define and created manually
├── namespace.yaml          # define and created manually
└── kustomization.yaml      # defined as blank so no reconcilation will happen
```

### Creating the sealed secret

```bash
kubectl create secret generic kafka-ui-secret \
  --from-literal=KAFKA_USERNAME=your-user \
  --from-literal=KAFKA_PASSWORD=your-password \
  --namespace kafka-ui \
  --dry-run=client -o yaml > temp.yaml


kubeseal --controller-namespace=kube-system --format yaml < temp.yaml > secret.yaml

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
