# dice-roll
This is a simple API that calls an existing postgreSQL database to retrieve the latest weather record.  This is a sample for doing no code OpenTelemetry, which simply means that there are no code changes within the code itself to track basic OpenTelemetry observability.
See further details on the Python code:  https://github.com/jaysuzi5/no-code-otel


## Project Structure

```bash
cluster/
  ├── apps/
  │   ├── no-code-otel/
  │   │   ├── configmap.yaml
  │   │   ├── deployment.yaml
  │   │   ├── postgresql-homelab-secret.yaml
  │   │   ├── service.yaml
  │   │   ├── kustomization.yaml
  │   │   └── weather-kustomization.py
  │   │   
```
