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