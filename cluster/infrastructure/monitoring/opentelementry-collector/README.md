# OpenTelemetry

### Initial Setup

The operator will deploy through kustomization.yaml:
https://github.com/open-telemetry/opentelemetry-operator/releases/latest/download/opentelemetry-operator.yaml


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
kubeseal --controller-namespace kube-system --format yaml < temp.yaml > secret.yaml
```



85adc306-aa08-430c-9db9-410f7e9697ae