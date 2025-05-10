# api.member
This is the corresponding K8s deployment for the login API.


1. login - API call that just takes a random user id
2. authenticate - API which is just an added layer to simulate a process that will do some high level processing and work flow direction of authentication.  This will randomly return some 401 unauthorized at around 5% rate.
   1. On a 401, it will create a Kafka event
3. member - API which will handle the bulk of the logic.
   1. Check if the member ID exists in a PostgreSQL database, if it does, then return the user as user id, first name, last name.  There is no realistic logic here or in the authenticate API, just simulated to see the calls to different systems.
   2. If the member is not found, treat it like a new member registration and do the following:
      1. Create a new member in PostgreSQL (will just use a random first and last name)
      2. Create a new record in MongoDB to simulate legacy process that might send out welcome information
      3. Raise an event to Kafka which might simulate a newer approach to an event model workflow.

See further details on the Python code:  https://github.com/jaysuzi5/api.login


## Project Structure

```bash
cluster/
  ├── apps/
  │   ├── api.authentication/
  │   │   ├── deployment.yaml
  │   │   ├── kustomization-flux.yaml
  │   │   ├── kustomization.yaml  
  │   │   ├── postgresql_secret.yaml  
  │   │   └── service.yaml
  │   │   
```
