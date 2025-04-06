## Homelab Splunk
Using Splunk primarily at this time to store logs

### Storage
See nfs folder for setup of NFS which this installation is expecting

### GitOps Structure

```bash
.
├── namespace.yaml
├── secret.yaml
├── pvc.yaml
├── deployment.yaml
├── service.yaml
├── kustomization.yaml
└── kustomization_flux.yaml
```

Note: Flux is having issues trying to reconcile storage class, so it is just here for documentation incase I need to
create it again, but it is set to skip and is not included in the kustomization file.

### Sealing the secrets

```bash
kubeseal --controller-namespace kube-system --format yaml < temp.yaml > secret.yaml
```


### Monitor installation and debug issues
Validate that the reconcilliation started:
```bash
flux logs
```

See details of kustomization logs
```bash
flux logs --kind=Kustomization --name=flux-system -n flux-system  --tail=10
```


See status of the reconcilliation:
```bash
kubectl get kustomization -n flux-system
```

Check if namespace was created
```bash
kubectl get ns
```

Review logs.  This can take some time for these to full provision
```bash
kubectl get pods -n splunk
```

Review logs.  This can take some time for these to full provision
```bash
kubectl get svc -n splunk
```

Go to Splunk front-end after updating the host file
http://splunk.local:30080

### Next Steps:
I need to setup a scheduled backup and define a location to back up the files.