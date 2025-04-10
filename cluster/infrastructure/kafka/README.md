# Kafka
Apache Kafka is an open-source distributed event streaming platform

See more at:  https://kafka.apache.org/

This will be a helm installation that will be tracked here, but will not be deployed by flux.  


### Storage
This will use storageClassName: nfs-client
See nfs folder for setup of NFS which this installation is expecting




### GitOps Structure
The following are stored here, but the kustomization.yaml is blank as this will be maintained in a consistent directory structue, but 
deployed out side of flux as with a lot of the existing infrasture

```bash
.
├── namespace.yaml          # define and created manually
└── kustomization.yaml      # defined as blank so no reconcilation will happen
```

### heml installation

```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update

helm install kafka bitnami/kafka -n kafka \
  --set global.storageClass=nfs-client \
  --set persistence.enabled=true \
  --set persistence.storageClass=nfs-client \
  --set persistence.size=5Gi \
  --set controllerReplicaCount=3 \
  --set kraft.enabled=true
```


### Validate Installation
Check the pods
```bash
kubectl get pods -n kafka
```

Output should be something like:
```bash
NAME                 READY   STATUS    RESTARTS   AGE
kafka-controller-0   1/1     Running   0          28s
kafka-controller-1   1/1     Running   0          28s
kafka-controller-2   1/1     Running   0          28s
```

Check the persistent volume claim:

```bash
kubectl get pvc -n kafka
```

Output should be something like:
```bash
NAME                      STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
data-kafka-controller-0   Bound    pvc-3975061f-b4ed-4c39-bf2a-1bada9811c12   8Gi        RWO            nfs-client     112s
data-kafka-controller-1   Bound    pvc-f4c34f5c-418a-4765-928f-8daf851349a4   8Gi        RWO            nfs-client     112s
data-kafka-controller-2   Bound    pvc-def2a488-b1c8-4da4-8d3a-a5540358579d   8Gi        RWO            nfs-client     112s
```

### Monitor installation and debug issues
Validate that the reconcilliation started:
```bash
flux logs
```

See details of kustomization logs
```bash
flux logs --kind=Kustomization --name=flux-system -n flux-system  --tail=10
```


See status of the reconcilliation:
```bash
kubectl get kustomization -n flux-system
```

Check if namespace was created
```bash
kubectl get ns
```

Review logs.  This can take some time for these to full provision
```bash
kubectl get pods -n splunk
```

Review logs.  This can take some time for these to full provision
```bash
kubectl get svc -n splunk
```

Go to Splunk front-end after updating the host file
http://splunk.local:30080

### Next Steps:
I need to setup a scheduled backup and define a location to back up the files.




Step 3: Install with values.yaml
bash
Copy
helm install kafka bitnami/kafka -n kafka -f kafka-values.yaml
Verification Steps
Check pod status (wait for all to be Running):

bash
Copy
kubectl get pods -n kafka -w
Verify persistent volumes:

bash
Copy
kubectl get pvc -n kafka
Check controller logs:

bash
Copy
kubectl logs kafka-controller-0 -n kafka
Important Notes
Client Configuration: You'll need to configure clients with SASL credentials. Here's a sample client properties file:

Copy
security.protocol=SASL_PLAINTEXT
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
  username="user" \
  password="bitnami";
Default Credentials: The Bitnami chart automatically creates these credentials:

Username: user

Password: Randomly generated (check with kubectl get secret kafka-passwords -n kafka -o jsonpath='{.data.client-passwords}' | base64 -d)

Custom Credentials: To set your own credentials, add to values.yaml:

yaml
Copy
auth:
  clientUser: myuser
  clientPassword: mypassword
Would you like me to provide any additional details about the configuration or suggest any modifications based on your specific requirements?