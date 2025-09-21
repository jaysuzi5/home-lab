# synology-collector
This is a simple python application to get basic synology information and logs it to Postgres by calling an local API


## Project Structure

```bash

cluster/
  ├── apps/
  │   ├── weather-app/
  │   │   ├── cron.yaml
  │   │   ├── namespace.yaml
  │   │   ├── kustomization.yaml
  │   │   └── synology-collector-kustomization.py
  │   │   
```


Created a secrets file with the values encoded
```bash
apiVersion: v1
kind: Secret
metadata:
  name: synlogy-collector-secrets
  namespace: collector
type: Opaque
data:
  NAS_USER: <base64-encoded-emporia-username>
  NAS_PASSWORD: <base64-encoded-emporia-password>
```

encoded the values with:
```bash
echo -n "<actual value>" | base64 
```

Then sealed the secret with:
```bash
kubeseal -f temp.yaml -o yaml > secrets.yaml  
```