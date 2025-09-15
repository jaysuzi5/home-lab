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


I ran the following on the controller, although this does not have to be.
Retrieved the certificate with:
```bash
kubectl get secret -n kube-system sealed-secrets-keyhhjt6 -o jsonpath='{.data.tls\.crt}' | base64 --decode > sealed-secrets-cert.pem
```

Created a secrets file with the values encoded
```bash
apiVersion: v1
kind: Secret
metadata:
  name: weather-secrets
  namespace: weather-app
type: Opaque
data:
  LATITUDE: <base64-encoded-latitude>
  LONGITUDE: <base64-encoded-longitude>
  OPENWEATHER_API_KEY: <base64-encoded-api-key>
  POSTGRES_PASSWORD: <base64-encoded-postgres-password>
  POSTGRES_USER: <base64-encoded-postgres-user>
```

encoded the values with:
```bash
echo -n "<actual value>" | base64 
```

Then sealed the secret with:
```bash
kubeseal --cert sealed-secrets-cert.pem -o yaml < temp.yaml > secret.yaml 
```


Monitor flux job to see that it was picked up:
```bash
 flux logs | tail
 ```

 Check detail status with:
 ```bash
 flux logs --kind=Kustomization --name=flux-system -n flux-system
 ```

 Check if namespace was creatd:
 ```bash
 k get namespace 
 ```

 Check if pods were created and running:
 ```bash
 k get pods -n weather-app
 ```

 If pod is crashing first check the details of the pod (update with specific pod name):
 ```bash
 k describe pod weather-collector-6d85df7d59-8p5p9 -n weather-app
 ```

 Next look at the logs (update with specific pod name):
 ```bash
 kubectl logs weather-collector-6bc8b468c6-qtddk  -n weather-app
 ```

 