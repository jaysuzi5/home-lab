# emporia-collector
This is a simple collector that pulls data from the Emporia APIs and loads it to a local database through 
a call to a local API.  The process will pull the current day and previous days daily metrics once an hour.  Future 
enhancements could reduce the number of pulls from the previous day, however, this will do a full day overlap to
ensure that no records are missed.  

This project is very specific to my Home Lab setup and uses a defined local API that is defined in a separate project. 
The API provides CRUD operations around my local PostgreSQL collection of this data.  More details can be found at: https://github.com/jaysuzi5-organization/emporia

This code is heavily borrowed from PyEmVue but simplified specific to my needs.  PyEmVue was not working for me with 
Python 3.12 and instead of running an older version, I pulled out or rewrote some portion to work for my needs.  I 
STRONGLY recommend using their version as it is a much more robust solution.
https://pypi.org/project/pyemvue/0.9.5/

## Project Structure

```bash

cluster/
  ├── apps/
  │   ├── weather-app/
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
  name: emporia-collector-secrets
  namespace: collector
type: Opaque
data:
  CLIENT_ID: <base64-encoded-emporia-client_id>
  PASSWORD: <base64-encoded-emporia-password>
  USERNAME: <base64-encoded-emporia-username>
```

encoded the values with:
```bash
echo -n "<actual value>" | base64 
```

Then sealed the secret with:
```bash
kubeseal --cert sealed-secrets-cert.pem -o yaml < temp.yaml > secret.yaml 
```