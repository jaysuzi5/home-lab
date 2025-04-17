# Kafka
Apache Kafka is an open-source distributed event streaming platform

See more at:  https://kafka.apache.org/

This will be a helm installation that will be tracked here, but will not be deployed by flux.  


### Storage
This will use storageClassName: nfs-client
See nfs folder for setup of NFS which this installation is expecting

### Sealed Secret
```bash
kubectl create secret generic kafka-user-credentials \
  --namespace kafka \
  --from-literal=username='user-name' \
  --from-literal=password='your-strong-password' \
  --dry-run=client -o yaml > temp.yaml

kubeseal -f temp.yaml -o yaml > secret.yaml

kubectl apply -f secret.yaml
```


### GitOps Structure
The following are stored here, but the kustomization.yaml is blank as this will be maintained in a consistent directory structue, but 
deployed out side of flux as with a lot of the existing infrasture

```bash
.
├── namespace.yaml          # define and created manually
├── secret.yaml             # define and created manually
├── values.yaml             # will use with the helm command
└── kustomization.yaml      # defined as blank so no reconcilation will happen
```

### heml installation

```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update

helm install kafka bitnami/kafka -n kafka -f values.yaml
```


to update:
```bash
helm upgrade kafka bitnami/kafka -n kafka -f values.yaml --reuse-values --force
```


if this does not pickup, you can restart the stateful set
```bash
kubectl -n kafka rollout restart statefulset kafka-controller
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


### Check the client

Client Configuration: You'll need to configure clients with SASL credentials. Here's a sample client properties file:

```bash
security.protocol=SASL_PLAINTEXT
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
  username="user" \
  password="bitnami";
Default Credentials: The Bitnami chart automatically creates these credentials:
```


Username: user
Password: Randomly generated (check with kubectl get secret kafka-passwords -n kafka -o jsonpath='{.data.client-passwords}' | base64 -d)

Custom Credentials: To set your own credentials, add to values.yaml:

```bash
auth:
  clientUser: myuser
  clientPassword: mypassword
```


### Test Python Application:

Create a pod to run it in:

```bash
kubectl run kafka-tester \
  --image=python:3.11-slim \
  -i --tty --rm \
  --namespace kafka \
  -- bash
```

Install nano for editing
```bash
apt-get update
apt-get install nano
```

Install confluent-kafka library
```bash
pip install confluent-kafka
```


Create a producer
```bash
from confluent_kafka import Producer
import time
import uuid

# Kafka configuration
conf = {
    'bootstrap.servers': 'kafka.kafka.svc.cluster.local:9092'
}

producer = Producer(conf)
topic = "test"
MESSAGES = 100

def delivery_report(err, msg):
    if err is not None:
        print(f"❌ Delivery failed: {err}")


for i in range(MESSAGES):
    message = {
        "id": str(uuid.uuid4()),
        "sequence": i,
        "content": f"This is message #{i}"
    }
    producer.produce(topic, value=str(message), callback=delivery_report)
    producer.poll(0)

# Wait for any outstanding messages
producer.flush()

print(f'{MESSAGES} messages were published')

```

Create a consumer
```bash
from confluent_kafka import Consumer, KafkaException, KafkaError

# Kafka configuration
conf = {
    'bootstrap.servers': 'kafka.kafka.svc.cluster.local:9092',
    'group.id': 'python-consumer-group',
    'auto.offset.reset': 'earliest'
}

consumer = Consumer(conf)

topic = "test"
consumer.subscribe([topic])

# Function to process messages
def consume_messages():
    RETRIES_MAX = 5
    retries = 0
    messages = 0
    try:
        while True:
            msg = consumer.poll(timeout=1.0)  # Wait for a message, timeout after 1 second
            
            if msg is None:  # No message available within timeout
                if retries < RETRIES_MAX:
                  retries += 1
                  continue
                else:
                  break
            if msg.error():
                if msg.error().code() == KafkaError._PARTITION_EOF:
                    print(f"End of partition reached: {msg.topic()} [{msg.partition()}] @ offset {msg.offset()}")
                else:
                    raise KafkaException(msg.error())
            else:
                messages += 1
    except KeyboardInterrupt:
        print("Consumer interrupted.")
    finally:
        consumer.close()
        print(f'Consumed {messages} messages')

# Start consuming messages
consume_messages()


```