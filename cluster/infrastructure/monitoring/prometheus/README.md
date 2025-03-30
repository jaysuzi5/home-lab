# prometheus
See further details at:  https://prometheus.io/
After installing through my GitOps with Kubernetes Manifests, I instead ran the helm, which installed Prometheus, Grafana, and Alertmanager
I will therefore remove the Kubernetes Manifest and just leave this readme file for future reference:

I have defined persistent data in my values.yaml

```bash 
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm install prometheus-operator prometheus-community/kube-prometheus-stack -n monitoring --create-namespace -f values.yaml
```


NOTES:
Check the status:
```bash
kubectl --namespace monitoring get pods
kubectl --namespace monitoring get pods -l "release=prometheus-operator"
```

Check the PVC:
```bash
kubectl get pvc -n monitoring
```

Check the PV:
```bash
kubectl get pv
```

I am storing the values.yaml here for consistency with other infrastructure that is released by Flux, however, I will use an empty kustomization file to ignore this file

I have updated the values for the pg-exporter-prometheus-postgres-exporter.default.svc.cluster.local:9187 and applied it with:

```bash
helm upgrade prometheus-operator prometheus-community/kube-prometheus-stack -n monitoring -f values.yaml
```

Whenever the upgrade is applied it will change the grafana service back to a clusterIP, so need to patch to a NodePort.  This may also change the port, which could be 
patched as well, but will leave it as dynamic at this time.

```bash
kubectl patch svc prometheus-operator-grafana -n monitoring -p '{"spec": {"type": "NodePort"}}'
```
