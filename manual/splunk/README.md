# Splunk
Using Splunk primarily at this time to store logs

### Storage
See nfs folder for setup of NFS which this installation is expecting

### Structure
THERE IS NO GITOPS.  This is being done manually

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

### Sealing the secrets

```bash
kubeseal --controller-namespace kube-system --format yaml < temp.yaml > secret.yaml
```

### Installation
```bash
k apply -f namespace.yaml
k apply -f secret.yaml 
k apply -f pvc.yaml 
k apply -f deployment.yaml
k apply -f service.yaml
```

Go to Splunk front-end after updating the host file
http://splunk.home:8000/en-US/app/launcher/home


### Removal
```bash
kubectl delete deployment splunk -n splunk
kubectl delete service splunk -n splunk
kubectl delete pvc splunk-pvc -n splunk
kubectl delete namespace splunk
```

### Fixing OpenTelementry after Reinstalling
1. Create index otel_logging. Settings -> Data -> Indices
2. Create HEC Token. Settings -> Data-> Data Input -> HTTP Event Collector
    2a. Global Settings -> All Tokens → Enabled
3. Update secret for OTEL Collector

Adding the Splunk token as a sealed secret.  Saving it hear if I need to re-apply, but have issues with 
SealedSecrets reconciling to it will not be part of the kustomization.yaml


```bash
echo -n 'your-token-here' | base64
```



```bash 
apiVersion: v1
kind: Secret
metadata:
  name: otel-collector-secrets
  namespace: monitoring
type: Opaque
data:
  SPLUNK_HEC_TOKEN: <base64-encoded-token-here>

```


```bash
kubeseal --controller-namespace kube-system --format yaml < temp.yaml > secret_for_otel.yaml
```




### Next Steps:
I need to setup a scheduled backup and define a location to back up the files.

