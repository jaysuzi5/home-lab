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


Flux was having difficulty in the order of operations, so created first with the following:
```bash
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
namespace: elasticsearch

resources:
  - namespace.yaml
  - secret.yaml

configMapGenerator:
  - name: elasticsearch-values
    files:
      - elasticsearch-values.yaml
  - name: kibana-values
    files:
      - kibana-values.yaml

generatorOptions:
  disableNameSuffixHash: true
```

Then updated to:
```bash
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
namespace: elasticsearch

resources:
  - elasticsearch-release.yaml
  - kibana-release.yaml
```
