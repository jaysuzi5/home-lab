# dice-roll
This is a simple API that calls an existing postgreSQL database to retrieve the latest weather record.  This is very similar to the no-code-otel implementation, except in this case, I am using ElasticSearch collector instead of my default ones which use Splunk for logs, Tempo for traces, and Prometheus for metrics.
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
