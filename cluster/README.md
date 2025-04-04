# Cluster

Monitoring commands:

View API Server Logs:

```bash
kubectl logs -n kube-system -l component=kube-apiserver
```

Flux Debugging

```bash
flux logs
```

```bash
flux get kustomizations
```

grep for specific details across all logs:

```bash
flux logs --all-namespaces | grep opentelemetry
```

force reconcillation:

```bash
flux reconcile kustomization flux-system
```

Port forwarding to 9090 for Prometheus Dashboard:

```bash
kubectl port-forward -n monitoring svc/prometheus-operator-kube-p-prometheus 9090  
```
http://localhost:9090/targets