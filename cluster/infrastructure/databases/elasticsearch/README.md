# elasticsearch
no sql database
See more information at:  https://www.elastic.co/enterprise-search


First create the certificates, created the namespace and save them as secrets:

```bash
mkdir -p elastic-ssl
cd elastic-ssl

# Generate CA private key
openssl genrsa -out ca.key 4096

# Generate CA certificate
openssl req -new -x509 -days 3650 -key ca.key -out ca.crt -subj "/CN=Elastic CA"

# Generate private key for Elasticsearch
openssl genrsa -out elasticsearch.key 4096

# Create CSR for Elasticsearch
openssl req -new -key elasticsearch.key -out elasticsearch.csr -subj "/CN=elasticsearch-master"

# Sign the CSR
openssl x509 -req -days 3650 -in elasticsearch.csr -CA ca.crt -CAkey ca.key -CAcreateserial -out elasticsearch.crt

# Generate private key for Kibana
openssl genrsa -out kibana.key 4096

# Create CSR for Kibana
openssl req -new -key kibana.key -out kibana.csr -subj "/CN=kibana"

# Sign the CSR
openssl x509 -req -days 3650 -in kibana.csr -CA ca.crt -CAkey ca.key -CAcreateserial -out kibana.crt

# Create Kubernetes secrets
kubectl create namespace elastic
kubectl create secret generic elastic-certificates --from-file=elasticsearch.crt --from-file=elasticsearch.key --from-file=ca.crt -n elastic
kubectl create secret generic kibana-certificates --from-file=kibana.crt --from-file=kibana.key --from-file=ca.crt -n elastic
```

Next, I will also use a sealed secret for the ElasticSearch password:

To create the sealed secret
```bash
# Create a local secret with all required fields
kubectl create secret generic elastic-credentials \
  --from-literal=password=YourSecurePassword \
  --from-literal=username=YourElasticUser \
  --namespace elastic \
  --dry-run=client -o yaml > elastic-credentials-secret.yaml

# Seal the secret
kubeseal --controller-namespace kube-system --format yaml < elastic-credentials-secret.yaml > elastic-credentials-sealedsecret.yaml

# Remove the temporary file
rm elastic-credentials-secret.yaml
```

I applied the namespace, certificates and sealed secrets first and will not include those in the kustomization files
I then applied the elastic, then kibana, then apm-server, and finally the controller

```bash
clusters/
└── infrastructure/
    └── databases/
        └── elasticsearch/
            ├── elastic-credentials-sealedsecret.yaml
            ├── elasticsearch-helmrepo.yaml
            ├── elasticsearch-helmrelease.yaml
            ├── kibana-helmrelease.yaml
            ├── kustomization.yaml                        
            └── kustomization_flux.yaml
```
