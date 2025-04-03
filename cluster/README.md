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


http://prometheus-operator-kube-p-prometheus.monitoring:9090/
       prometheus-operator-kube-p-prometheus