# enphase-collector
This is a simple collector that pulls data from the Enphase APIs and loads it to a local database through 
a call to a local API.  The process will pull the current summary, events, and alarms and will be scheduled through
a Kubernetes CRON to run every 10 minutes.

I will be taking a new approach to handling the tokens as they cannot just be renewed every cycle.  The enphase APIs
require that you manually approve when creating an access code the first time.  The access token is good for one day
with the refresh token being good for 30 days.  However, when you refresh, you get a new access token and refresh 
token.  Therefore, you will not require manual approval unless the refresh token is stall or you add a brand new
access token.  

The strategy will be to refresh the access token every 12 hours to be safe and store the updated access and refresh
token.  Since this will be running on Kubernetes, I will read direct and update direct from a Kubernetes secret.  I 
will NOT send them in through an environment variable.  This approach may not work for you if you are cloning this 
code and you would need to adjust the code accordingly.

This code will also use my local enphase API which exposes the CRUD operations around a Postgres database that I
will be storing the data in.  See that repo for additional details. https://github.com/jaysuzi5-organization/enphase

## Project Structure

```bash

cluster/
  ├── apps/
  │   ├── weather-app/
  │   │   ├── cron.yaml
  │   │   ├── namespace.yaml
  │   │   ├── service-account.yaml  
  │   │   ├── kustomization.yaml
  │   │   └── enphase-collector-kustomization.py
  │   │   
```

Created a secrets file with the values encoded
```bash
apiVersion: v1
kind: Secret
metadata:
  name: enphase-tokens
  namespace: collector
type: Opaque
stringData:
  api_key: <<api_key_value>>
  user_id: <<user_id_value>>
  client_secret: <<client_secret_value>>
  access_token: <<access_token_value>>
  refresh_token: <<refresh_token_value>>
  last_updated: "2025-09-18T00:00:00Z"
```

encoded the values with:
```bash
echo -n "<actual value>" | base64 
```

