# homelab-hub
A Django application that is used to share information about the homelab.  The start of a centralized developer platform.

## Project Structure

```bash

cluster/
  ├── apps/
  │   ├── homelab-hub/
  │   │   ├── configmap.yaml
  │   │   ├── deployment.yaml
  │   │   ├── ingress.yaml
  │   │   ├── namespace.yaml
  │   │   ├── roles.yaml    
  │   │   ├── secrets.yaml  
  │   │   ├── service.yaml    
  │   │   ├── kustomization.yaml
  │   │   └── homelab-hub-kustomization.py
  │   │   
```


Created a secrets file with the values encoded
```bash
apiVersion: v1
kind: Secret
metadata:
  name: homelab-hub-secrets
  namespace: homelab-hub
type: Opaque
stringData:
  POSTGRES_USER: user_name
  POSTGRES_PASSWORD: password
  SECRET_KEY: secret
```


Then sealed the secret with:
```bash
kubeseal -f temp.yaml -o yaml > secrets.yaml  
```



