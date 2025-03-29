# metalLB
MetalLB is a load-balancer implementation for bare metal Kubernetes clusters, using standard routing protocols.
See more information: https://metallb.io/


Installed with helm
```bash
helm repo add metallb https://metallb.github.io/metallb
helm repo update
helm install metallb metallb/metallb --namespace metallb-system --create-namespace
```

The following directory contains additional configuration for it