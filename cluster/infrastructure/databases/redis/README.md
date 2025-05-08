
# redis
This is an extremely simple installation of redis just for testing with no authentication enabled.  This will be a complete manual helm based installation. 




## Project Structure

```bash
clusters/
└── infrastructure/
    └── databases/
        └── redis/
            ├── namespace.yaml              -- applied manually
            ├── values.yaml                 -- applied manually with helm install
            ├── kustomization.yaml          -- only exist so that the files will be ignored

```

Get helm chart and initial default values
``` bash
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update

helm show values bitnami/redis > values.yaml
```

Updated values.yaml: Since this is only for testing and not a production environment, I made the following to updates for simplicity
1. set auth.enabled = false
2. set persistence = false for master and replica


Full install
``` bash
helm install redis bitnami/redis -f values.yaml -n redis
```

Full uninstall

``` bash
helm uninstall redis -n redis 
```