## Homelab PostgreSQL Database
This will be a shared database by multiple small applications that I run in my homelab.  For convience, I want to have one database instead of a number of smaller databases that I would need to setup, monitor, and backup separately.  

### Storage
Will be defined as an NFS storage class that will live on my Synology NAS.  This was setup first with the following

```bash
helm repo add nfs-subdir-external-provisioner https://kubernetes-sigs.github.io/nfs-subdir-external-provisioner/
helm repo update
helm install nfs-provisioner nfs-subdir-external-provisioner/nfs-subdir-external-provisioner   --set nfs.server=192.168.86.210   --set nfs.path=/volume2/k8s-data/postgresql-homelab-storage   --set storageClass.name=postgresql-homelab-storage   --set storageClass.reclaimPolicy=Retain
```
and can be confirmed with

```bash
kubectl get storageclass
```

### CloudNativePG
I am using CloudNativePG to setup a cluster for the database

Installation:
```bash
kubectl apply --server-side -f \
  https://raw.githubusercontent.com/cloudnative-pg/cloudnative-pg/release-1.25/releases/cnpg-1.25.1.yaml
```

Additional Documentation for CloudNativePG:  https://cloudnative-pg.io/documentation/current/#

### GitOps Structure

```bash
.
├── namespace.yaml
├── postgresql-homelab.yaml
├── secrets.yaml
├── kustomization.yaml
└── kustomization_flux.yaml
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

### Next Steps:
I need to setup a scheduled backup and define a location to back up the files.