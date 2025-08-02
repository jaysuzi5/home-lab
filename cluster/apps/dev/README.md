# NAMESPACE: dev
This section maintains some common settings for applications that run under this namespace.  These apps will be deployed via Backstage.io and use
github workflows and Argo for deployment, so their details will be separate from this home-lab repository.  However, things like secrets that
can apply to multiple different APIs will be maintain here


```bash
.
└── secrets.yaml
```

seal the secret:
```bash
kubeseal -f temp.yaml -o yaml > secrets.yaml 
```