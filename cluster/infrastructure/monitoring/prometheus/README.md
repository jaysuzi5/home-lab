# prometheus
Standard Prometheus installation using Kustomizatoin and Flux
See further details at:  https://prometheus.io/


## Project Structure

```bash

cluster/
  ├── infrastructure/
  │   ├── monitoring/
  │   │   ├── prometheus
  │   │   │   ├── configmap.yaml
  │   │   │   ├── deployment.yaml
  │   │   │   ├── deployment.yaml
  │   │   │   ├── kustomization_flux.yaml
  │   │   │   ├── kustomization.yaml
  │   │   │   ├── namespace.yaml
  │   │   │   ├── pvc.yaml
  │   │   │   ├── service-monitor.yaml
  │   │   │   └── service.py
  │   │   
```

Monitor flux job to see that it was picked up:
```bash
 flux logs | tail
 ```

 Check detail status with:
 ```bash
 flux logs --kind=Kustomization --name=flux-system -n flux-system
 ```

 Check if namespace was creatd:
 ```bash
 k get namespace 
 ```

 Check if pods were created and running:
 ```bash
 k get pods -n monitoring
 ```

 If pod is crashing first check the details of the pod (update with specific pod name):
 ```bash
 k describe pod weather-collector-6d85df7d59-8p5p9 -n monitoring
 ```

 Next look at the logs (update with specific pod name):
 ```bash
 kubectl logs weather-collector-6bc8b468c6-qtddk  -n monitoring
 ```

 
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm install prometheus-operator prometheus-community/kube-prometheus-stack -n monitoring --create-namespace

NAME: prometheus-operator
LAST DEPLOYED: Thu Mar 27 18:40:58 2025
NAMESPACE: monitoring
STATUS: deployed
REVISION: 1
NOTES:
kube-prometheus-stack has been installed. Check its status by running:
  kubectl --namespace monitoring get pods -l "release=prometheus-operator"

Get Grafana 'admin' user password by running:

  kubectl --namespace monitoring get secrets prometheus-operator-grafana -o jsonpath="{.data.admin-password}" | base64 -d ; echo

Access Grafana local instance:

  export POD_NAME=$(kubectl --namespace monitoring get pod -l "app.kubernetes.io/name=grafana,app.kubernetes.io/instance=prometheus-operator" -oname)
  kubectl --namespace monitoring port-forward $POD_NAME 3000