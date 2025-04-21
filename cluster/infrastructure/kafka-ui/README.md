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
helm repo add kafka-ui https://provectus.github.io/kafka-ui-charts
helm repo update

helm install kafka-ui kafka-ui/kafka-ui -n kafka-ui -f values.yaml
```

to update:
```bash
helm upgrade kafka-ui kafka-ui/kafka-ui -n kafka-ui -f values.yaml
```

if this does not pickup, you can do a restart
```bash
kubectl -n kafka-ui rollout restart deployment kafka-ui
```

Results:
```bash
1. Get the application URL by running these commands:
  export POD_NAME=$(kubectl get pods --namespace kafka-ui -l "app.kubernetes.io/name=kafka-ui,app.kubernetes.io/instance=kafka-ui" -o jsonpath="{.items[0].metadata.name}")
  echo "Visit http://127.0.0.1:8080 to use your application"
  kubectl --namespace kafka-ui port-forward $POD_NAME 8080:8080
```

Instead of using the port forwarding, I manually created the service.  See the service.yaml.