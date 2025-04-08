# metalLB
MetalLB is a load-balancer implementation for bare metal Kubernetes clusters, using standard routing protocols.
See more information: https://metallb.io/


Installed with helm
```bash
helm repo add metallb https://metallb.github.io/metallb
helm repo update

helm install metallb metallb/metallb --namespace metallb-system --create-namespace -f
```

Just storing README.md with instructions and ippool.yaml, but applying it manually after the helm installation

```bash

cluster/
  ├── infrastructure/
  │   ├── metallib/
  │   │   ├── kustomization.yaml   # Just to ignore the ippool.yaml
  │   │   └── ippool.py
  │   │   
```