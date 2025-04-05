# tempo
Grafana Tempo is an open-source, easy-to-use, and high-scale distributed tracing backend. Tempo lets you search for traces, generate metrics from spans, 
and link your tracing data with logs and metrics.

See more at:  https://grafana.com/docs/tempo/



I have defined persistent data in my values.yaml

```bash 
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
helm install tempo grafana/tempo -n monitoring -f values.yaml
```

Note:  storing the values file here, however need to have an empty kustomizaiton file so that flux reconcilation does not fail and try to bring this in


reapply the values.yaml on update

```bash
helm upgrade tempo grafana/tempo -n monitoring -f values.yaml
```


