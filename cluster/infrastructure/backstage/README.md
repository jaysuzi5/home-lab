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
kubeseal --cert sealed-secrets-cert.pem -o yaml < secret.yaml > sealed-secret.yaml
```


