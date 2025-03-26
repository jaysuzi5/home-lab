# weather-collector
This is a simple application that makes two calls to the OpenWeather weather API for a given location. The first call 
gets the current weather details while the second call gets a 5-day forecast.  Some logic is applied to the data and
then saved to a PostgreSQL database.  This mainly serves as a test application that I can use to work with Kubernetes 
and Docker.  I will use this to practice DevOps using Flux to sync changes to my homelab.  

See further details at:  https://github.com/jaysuzi5/weather-collector

Future planned enhancements include:
<ul>
<li>Grafana Dashboard to display the data</li>
<li>Using OpenTelementry for logs, metrics, and traces</li>
<li>Use a Kubernetes CRON instead of a sleep</li>
<li>Consistent logging with Anomaly Detection and Alerting</li>
</ul>

## Project Structure

```bash

cluster/
  ├── apps/
  │   ├── weather-app/
  │   │   ├── namespace.yaml
  │   │   ├── secrets.yaml
  │   │   ├── configmap.yaml
  │   │   ├── deployment.yaml
  │   │   ├── kustomization.yaml
  │   │   └── weather-kustomization.py
  │   │   
```

Seal the secret with:

```bash
kubeseal --cert sealed-secrets-cert.pem -o yaml < temp.yaml > secrets.yaml
```

If needed, you can retrieve the secret with:
```bash
kubectl get secret -n kube-system sealed-secrets-keyhhjt6 -o jsonpath='{.data.tls\.crt}' | base64 --decode > sealed-secrets-cert.pem
```