# Kubernetes Dasbhoard
I have Kubernetes Dashboard previously installed, but I want to expose it to my local network.

First, I need to create a certificate.  However, since this is just my local network, I will use a self-signed certficate
```bash
openssl req -x509 -newkey rsa:2048 -keyout dashboard.key -out dashboard.crt -days 3650 -nodes -subj '/CN=kubernetes-dashboard.local'
```

I then need to create the key and secret using the dashboard.key and dashboard.crt that were created:

```bash
kubectl create secret tls dashboard-tls \
  --namespace kubernetes-dashboard \
  --cert=dashboard.crt \
  --key=dashboard.key
  ```

After having issues with metalLB, I have decided just to run using NodePort, but needed to patch the Grafana service from ClusterIP to NodePort

```bash
kubectl patch svc kubernetes-dashboard -n kubernetes-dashboard -p '{"spec": {"type": "NodePort"}}'
```

I then setup an ingress rule (ingress.yaml)