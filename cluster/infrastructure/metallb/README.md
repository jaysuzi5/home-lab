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

```bash

cluster/
  ├── infrastructure/
  │   ├── metallib/
  │   │   ├── ip-address-pool.yaml
  │   │   ├── kustomization-flux.yaml
  │   │   ├── kustomization.yaml
  │   │   └── l2-advertisement.py
  │   │   
```

Validate the installation:

Check the ingress server to make sure it is a LoadBalancer and has has an external IP that matches a machine in your cluster

```bash
kubectl get service -n kube-system ingress-nginx-controller -o wide
```

Check the MetalLB pods are running:

```bash
kubectl get pods -n metallb-system
```

Check the IP addresses are correct:

```bash
kubectl get ipaddresspool -n metallb-system
kubectl describe ipaddresspool -n metallb-system main-pool 
```

Check the L2 advertisement:

```bash
kubectl get l2advertisement -n metallb-system
kubectl describe l2advertisement -n metallb-system l2-adv
```

I also validated that I now have an address for my Grafana ingress

```bash
k get ingress -n monitoring
```

After mapping the IP in my local host file

```bash
192.168.86.200  grafana.local
```

I can now hit grafana without having to use a port:  
http://grafana.local/