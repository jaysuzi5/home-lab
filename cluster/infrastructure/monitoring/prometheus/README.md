# prometheus
See further details at:  https://prometheus.io/
After installing through my GitOps with Kubernetes Manifests, I instead ran the helm, which installed Prometheus, Grafana, and Alertmanager
I will therefore remove the Kubernetes Manifest and just leave this readme file for future reference:


```bash 
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm install prometheus-operator prometheus-community/kube-prometheus-stack -n monitoring --create-namespace
```


NOTES:
kube-prometheus-stack has been installed. Check its status by running:
```bash
kubectl --namespace monitoring get pods -l "release=prometheus-operator"
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

