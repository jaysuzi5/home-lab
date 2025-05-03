# ElasticSearch
See more information at:  https://www.elastic.co/enterprise-search

After some failed attempts on getting SSL working between ElasticSearch, Kibana, and APM-Server using a GitOps installation, I am going to try a straightforward helm installation and let helm manage the certificates.

First generate the required certificates
```bash
mkdir -p /tmp/elastic-certs
cd /tmp/elastic-certs

openssl req -x509 -nodes -days 3650 -newkey rsa:2048 \
  -keyout ca.key -out ca.crt \
  -subj "/CN=Elasticsearch CA"

# Generate certificates
openssl req -nodes -newkey rsa:2048 \
  -keyout tls.key -out tls.csr \
  -subj "/CN=elasticsearch-master"
openssl x509 -req -days 3650 -in tls.csr \
  -CA ca.crt -CAkey ca.key -CAcreateserial -out tls.crt
```


If full cleaneup is needed
```bash
helm uninstall kibana -n elasticsearch
helm uninstall elasticsearch -n elasticsearch               
kubectl delete pvc -n elasticsearch --all
kubectl delete namespace elasticsearch
```

The move onto the installation
```bash
helm repo add elastic https://helm.elastic.co
helm repo update
```

Create the namespace and secret for the certificate
```bash
kubectl create namespace elasticsearch

kubectl create secret generic elastic-certificates -n elasticsearch \
  --from-file=ca.crt \
  --from-file=tls.crt \
  --from-file=tls.key
```

Create a secret for the default elasticsearch user - use your own values
```bash
echo -n 'elastic' | base64
echo -n 'supersecret' | base64
```

Create a file secret.yaml
```bash
apiVersion: v1
kind: Secret
metadata:
  name: elastic-credentials
  namespace: elasticsearch
type: Opaque
data:
  username: ZWxhc3RpYw==         # base64-encoded username, e.g., "elastic"
  password: c3VwZXJzZWNyZXQ=     # base64-encoded password, e.g., "supersecret"
```

Apply the secret
```bash
k apply -f secret.yaml
```

Install elasticsearch
```bash
helm upgrade --install elasticsearch elastic/elasticsearch \
  --namespace elasticsearch \
  --version "7.17.1" \
  --values elasticsearch-values.yaml
```

Once Elastic is up and stable, I moved onto Kibana

```bash
helm upgrade --install kibana elastic/kibana \
  --namespace elasticsearch \
  --version "7.17.1" \
  --values kibana-values.yaml
```

Then to apm-server

```bash
helm upgrade --install apm-server elastic/apm-server \
  --namespace elasticsearch \
  --version "7.17.1" \
  --values apm-server-values.yaml
```

If you need to uninstall and do a clean install
```bash
helm uninstall apm-server -n elasticsearch
```

Finally, I am going to set up the OTEL collector, which I will do with the following manifest
```
k apply -f collector.yaml
```

If a full cleanup is needed, the following can be run
```bash
kubectl delete deployment otel-collector-elasticsearch -n elasticsearch
kubectl delete configmap otel-collector-config -n elasticsearch
kubectl delete service otel-collector-elasticsearch -n elasticsearch
```