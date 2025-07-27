# backstage.io
This will be a kubernettes deployment with Flux


```bash
.
├── namespace.yaml
├── secret.yaml
├── deployment.yaml 
└── kustomization.yaml
```

seal the secret:
```bash
kubeseal -f temp.yaml -o yaml > secrets.yaml 
```