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
```



Get Grafana 'admin' user password by running:
```bash
kubectl --namespace monitoring get secrets prometheus-operator-grafana -o jsonpath="{.data.admin-password}" | base64 -d ; echo
```

Access Grafana local instance:
```bash
  export POD_NAME=$(kubectl --namespace monitoring get pod -l "app.kubernetes.io/name=grafana,app.kubernetes.io/instance=prometheus-operator" -oname)
  kubectl --namespace monitoring port-forward $POD_NAME 3000
```


I am storing the values.yaml here for consistency with other infrastructure that is released by Flux, however, I will use an empty kustomization file to ignore this file