# prometheus
Standard Prometheus installation using Kustomizatoin and Flux
See further details at:  https://prometheus.io/


## Project Structure

```bash

cluster/
  ├── infrastructure/
  │   ├── observability/
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
 k get pods -n observability
 ```

 If pod is crashing first check the details of the pod (update with specific pod name):
 ```bash
 k describe pod weather-collector-6d85df7d59-8p5p9 -n observability
 ```

 Next look at the logs (update with specific pod name):
 ```bash
 kubectl logs weather-collector-6bc8b468c6-qtddk  -n observability
 ```

 