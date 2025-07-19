# backstage.io
This will be a helm installation for a backstage.io, but all artifacts will be stored here

## Project Structure

```bash

cluster/
  ├── infrastructure/
  │   ├── backstage/
  │   │   ├── namespace.yaml
  │   │   ├── secrets.yaml
  │   │   └── values.py
  │   │   
```


namespace for backstage:  backstage
database details:
   host: 192.168.86.200
   port: 30001
   username: from backstage-secrets
   password: from backstage-secrets
  

Created a secrets file with the values encoded
```bash
apiVersion: v1
kind: Secret
metadata:
  name: backstage-secrets
  namespace: backstage
type: Opaque
data:
  POSTGRES_PASSWORD: <base64-encoded-postgres-password>
  POSTGRES_USER: <base64-encoded-postgres-user>
```


encoded the values with:
```bash
echo -n "<actual value>" | base64 
```

Then sealed the secret with:
```bash
kubeseal --controller-namespace kube-system --format yaml < temp.yaml > sealed-postgres-secret.yaml
```


I then installed backstage via helm:

```bash
helm repo add backstage https://backstage.github.io/charts
helm repo update

helm install backstage backstage/backstage \
  -n backstage \
  -f values.yaml
```



kubectl run backstage-migrate \
  --rm -i -t \
  --image=ghcr.io/backstage/backstage:latest \
  --restart=Never \
  -n backstage \
  -- /bin/sh
