# weather-collector
This is a simple application that makes two calls to the OpenWeather weather API for a given location. The first call 
gets the current weather details while the second call gets a 5-day forecast.  Some logic is applied to the data and
then saved to a PostgreSQL database.  This mainly serves as a test application that I can use to work with Kubernetes 
and Docker.  I will use this to practice DevOps using Flux to sync changes to my homelab.  


## Project Structure

```bash

cluster/
  ├── apps/
  │   ├── weather-collector/
  │   │   ├── cron.yaml
  │   │   ├── namespace.yaml
  │   │   ├── secrets.yaml
  │   │   ├── kustomization.yaml
  │   │   └── emporia-collector-kustomization.py
  │   │   
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
