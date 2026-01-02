## Generic Postgresql Instance
This install will contain multiple different small database for applications that can share one instance

### Storage
See nfs folder for setup of NFS which this installation is expecting

### CloudNativePG
See postgresql/homelab README.md for futher details


### GitOps Structure

```bash
.
├── namespace.yaml
├── deployment.yaml
├── secrets.yaml
├── kustomization.yaml
└── kustomization_flux.yaml
```


I also expose all of the postgres instances with a NodePort so that I can connect easily with pgAdmin from my development machine.  To
check which NodePorts have already been defined, I can run:

```bash
kubectl get svc --all-namespaces | grep NodePort
```

### Sealing the Secrets

First create secrets that will be used for the superuser.  These can different between dev and prod, but for simplicity, I will just show one:

temp.yaml for the secret in the format:
```bash
apiVersion: v1
data:
  password: <base64-encoded-emporia-password>
  username: <base64-encoded-emporia-username>
kind: Secret
metadata:
  creationTimestamp: "2026-01-01T14:20:41Z"
  name: postgresql-<<aplication_name>>
  namespace: postgresql
type: Opaque
```


encoded the values with:
```bash
echo -n "<actual value>" | base64 
```

Then sealed the secret with:
```bash
kubeseal -f temp.yaml -o yaml > secrets-postgres.yaml  
```

Apply the secret with:
```bash
k apply -f secrets-postgres.yaml  
```

Validate secrets:
```bash
kubectl get secret postgresql -n postgresql -o yaml | grep username | awk '{print $2}' | base64 --decode
kubectl get secret postgresql -n postgresql -o yaml | grep password | awk '{print $2}' | base64 --decode
```

### Create the Manifest for the postgresql Cluster and NodePort
```bash
k apply -f deployment.yaml
```

Validate it is running:
1. Find your PostgreSQL pod
```bash
k get pods -n postgresql
```
2. Exec into the pod

```bash
k exec -it postgresql-1 -n postgresql -- bash
```

3. Log into PostgreSQL
```bash
psql -U postgres
```

4. List all users/roles
```bash
\du
```

5. To give access from pgAdmin, use the owner that was created, however, you need to reset the owner's password and give them permission
```bash
ALTER USER jcurtis WITH PASSWORD '<<password>>';
ALTER USER jcurtis WITH SUPERUSER CREATEDB CREATEROLE REPLICATION BYPASSRLS;
```

### Connect using pgAdmin
Register -> Server
- Name: Kubernettes
- Host: 192.168.86.200
- Port: 30004
- Database: postgres
- Username: jcurtis
- Password: <<password>>