# OpenTelemetry

### Initial Setup

The operator will deploy through kustomization.yaml:
https://github.com/open-telemetry/opentelemetry-operator/releases/latest/download/opentelemetry-operator.yaml


Adding the Splunk token as a sealed secret.  Saving it hear if I need to re-apply, but have issues with 
SealedSecrets reconciling to it will not be part of the kustomization.yaml
```bash
kubeseal --controller-namespace kube-system --format yaml < temp.yaml > secret.yaml
```