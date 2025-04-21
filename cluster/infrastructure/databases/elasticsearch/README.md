# elasticsearch
no sql database
See more information at:  https://www.elastic.co/enterprise-search


This will be a first attempt at doing a full helm installation via GitOps/Flux deployment

```bash
clusters/
└── infrastructure/
    └── databases/
        └── elasticsearch/
            ├── namespace.yaml
            ├── elasticsearch-values.yaml
            ├── kibana-values.yaml
            ├── helmrepository.yaml
            ├── elasticsearch-release.yaml
            ├── kibana-release.yaml
            ├── secret.yaml
            ├── apm-server-release.yaml
            ├── apm-server-values.yaml            
            ├── kustomization.yaml                        
            └── kustomization_flux.yaml
```

Create a sealed secret with the following

```bash
kubectl create secret generic elastic-credentials \
  --from-literal=password='changeme' \
  --namespace=elasticsearch \
  --dry-run=client -o yaml | \
  kubeseal --format=yaml > elastic-secret.yaml
```

Update:  Adding APM Server to test ElasticSearch with OpenTelemetry