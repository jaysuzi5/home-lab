# Grafana
After installing through my GitOps with Kubernetes Manifests, I instead ran the helm, which installed Prometheus, Grafana, and Alertmanager.
See the prometheus for details on the helm installation

I was able to access Grafana locally by running a port forwarder.  However, I wanted to make access a bit easier, so I want to set up an Ingress.
Since this will be the first ingress on my cluster, I needed to setup the ingress controller

```bash
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update
helm install ingress-nginx ingress-nginx/ingress-nginx --namespace kube-system
```

I then created the ingress.yaml manifest, but I need to use a fake DNS (not IP) of grafana.local
and then edit my local host file with the mapping
