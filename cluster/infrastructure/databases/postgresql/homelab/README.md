## Homelab PostgreSQL Database
This will be a shared database by multiple small applications that I run in my homelab.  For convience, I want to have one database instead of a number of smaller databases that I would need to setup, monitor, and backup separately.  

### Storage
Will be defined as an NFS storage class that will live on my Synology NAS.  This was setup first with the following

```bash
helm install nfs-client nfs-subdir-external-provisioner/nfs-subdir-external-provisioner \
--set nfs.server="192.168.86.210" \
--set nfs.path="/volume2/k8s-data" -n default
```

### CloudNativePG
I am using CloudNativePG controller to setup a cluster for the database

Installation:
```bash
helm install cloudnative-pg cloudnative-pg/cloudnative-pg -n cloudnative-pg
```

Additional Documentation for CloudNativePG:  https://cloudnative-pg.io/documentation/current/#

### GitOps Structure

```bash
.
├── namespace.yaml
├── postgresql-homelab.yaml
├── secrets.yaml
├── storageclass.yaml
├── kustomization.yaml
└── kustomization_flux.yaml
```

Note: Flux is having issues trying to reconcile storage class, so it is just here for documentation incase I need to
create it again, but it is set to skip and is not included in the kustomization file.

### Sealing the secrets

```bash
kubeseal --controller-namespace kube-system --format yaml < secrets.yaml > sealed-postgres-secret.yaml
```

### Monitor installation and debug issues
Validate that the Custom Resource is available
```bash
kubectl get crd
```

Validate that the reconcilliation started:
```bash
flux logs
```

See details of kustomization logs
```bash
flux logs --kind=Kustomization --name=flux-system -n flux-system
```


See status of the reconcilliation:
```bash
kubectl get kustomization -n flux-system
```

Check if namespace was created
```bash
kubectl get ns
```

Switch to the namespace to make other commands simplier
```bash
kubectl config set-context --current --namespace=postgresql-homelab
```

Review logs.  This can take some time for these to full provision
```bash
kubectl get pods
```

When using the default password, you can retrieve the password with the following:
```bash
kubectl get secret postgresql-cluster-app -n postgresql-homelab -o jsonpath='{.data.password}' | base64 --decode
```


### Next Steps:
I need to setup a scheduled backup and define a location to back up the files.