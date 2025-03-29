# Grafana
After installing through my GitOps with Kubernetes Manifests, I instead ran the helm, which installed Prometheus, Grafana, and Alertmanager.
See the prometheus for details on the helm installation

I was able to access Grafana locally by running a port forwarder.  However, I wanted to make access a bit easier, which I was able to do by patching
the helm installation to change the service to a nodePort

```bash
kubectl patch svc prometheus-operator-grafana -n monitoring -p '{"spec": {"type": "ClusterIP"}}'
```
