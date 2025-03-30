# Grafana
After installing through my GitOps with Kubernetes Manifests, I instead ran the helm, which installed Prometheus, Grafana, and Alertmanager.
See the prometheus for details on the helm installation



I was able to access Grafana locally by running a port forwarder.  However, I wanted to make access a bit easier.

First, I setup nginx ingress (see ingress-nginx folder for additiona details)

I then setup an ingress rule (ingress.yaml)

After having issues with metalLB, I have decided just to run using NodePort, but needed to patch the Grafana service from ClusterIP to NodePort

```bash
kubectl patch svc prometheus-operator-grafana -n monitoring -p '{"spec": {"type": "NodePort"}}'
```



Get Grafana 'admin' user password by running:
```bash
kubectl --namespace monitoring get secrets prometheus-operator-grafana -o jsonpath="{.data.admin-password}" | base64 -d ; echo
```

Access Grafana local instance:
```bash
  export POD_NAME=$(kubectl --namespace monitoring get pod -l "app.kubernetes.io/name=grafana,app.kubernetes.io/instance=prometheus-operator" -oname)
  kubectl --namespace monitoring port-forward $POD_NAME 3000
```

