## HomeLab-Hub PostgreSQL Database
Creating a new Postgresql database to be used by HomeLab-Hub, the Django front end.

### Storage
See nfs folder for setup of NFS which this installation is expecting

### CloudNativePG
See postgresql/homelab README.md for futher details


### GitOps Structure

```bash
.
├── namespace.yaml
├── postgresql-homelab-hub.yaml
├── secrets.yaml
├── kustomization.yaml
└── kustomization_flux.yaml
```


### Sealing the secrets

temp.yaml for the secret in the format:
```bash
apiVersion: v1
data:
  password: <base64-encoded-emporia-password>
  username: <base64-encoded-emporia-username>
kind: Secret
metadata:
  creationTimestamp: "2025-07-19T14:20:41Z"
  name: postgresql-cluster-superuser
  namespace: postgresql-homelab-hub
type: Opaque

```


encoded the values with:
```bash
echo -n "<actual value>" | base64 
```

Then sealed the secret with:
```bash
kubeseal -f temp.yaml -o yaml > secrets.yaml  
```


## Checking the Server:

1. Find your PostgreSQL pod
```bash
k get pods -n postgresql-homelab-hub
```
2. Exec into the pod

```bash
k exec -it postgresql-homelab-hub-1 -n postgresql-homelab-hub -- bash
```

3. Log into PostgreSQL
```bash
psql -U postgres
```

If you need a password, you can fetch it from the Kubernetes secret:
```bash
kubectl get secret -n postgresql-homelab-hub postgresql-homelab-hub -o jsonpath="{.data.postgres-password}" | base64 -d
```

4. List all users/roles
```bash
\du
```

5. Reset a user’s password
```bash
ALTER USER myuser WITH PASSWORD 'new_secure_password';
```

6. Give a user full control or adjust as needed
```bash
ALTER ROLE myuser SUPERUSER CREATEROLE CREATEDB REPLICATION BYPASSRLS;
```

7. Exit
```bash
\q
```

then exit the pod shell.