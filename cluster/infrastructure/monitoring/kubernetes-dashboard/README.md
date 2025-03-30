# Kubernetes Dasbhoard
I have Kubernetes Dashboard previously installed, but I want to expose it to my local network.


After having issues with metalLB, I have decided just to run using NodePort, but needed to patch the Grafana service from ClusterIP to NodePort

```bash
kubectl patch svc kubernetes-dashboard -n kubernetes-dashboard -p '{"spec": {"type": "NodePort"}}'
```


Creating a local CA and signed certificate:

```bash
openssl genpkey -algorithm RSA -out my-ca.key -pkeyopt rsa_keygen_bits:2048
openssl req -x509 -new -nodes -key my-ca.key -sha256 -days 3650 -out my-ca.crt -subj "/CN=my-local-ca"
```

Open the Keychain Access application and drag the my-ca.crt file into the "System" keychain.  Set the certificate to be trusted for SSL.


Generate a private key for the dashboard
```bash
openssl genpkey -algorithm RSA -out dashboard.key -pkeyopt rsa_keygen_bits:2048
```

Create a signing request:
```bash
openssl req -new -key dashboard.key -out dashboard.csr -subj "/CN=kubernetes-dashboard.local"
```

Sign the certificate:
```bash
openssl x509 -req -in dashboard.csr -CA my-ca.crt -CAkey my-ca.key -CAcreateserial -out dashboard.crt -days 3650 -sha256
```

Create a secret:
```bash
kubectl create secret tls dashboard-tls \
  --namespace kubernetes-dashboard \
  --cert=dashboard.crt \
  --key=dashboard.key \
  --dry-run=client -o yaml | kubectl apply -f -
```

Retrieve the Token:
kubectl create token dashboard-admin-sa --namespace=kube-system

