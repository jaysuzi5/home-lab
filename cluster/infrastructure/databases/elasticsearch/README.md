# ElasticSearch
See more information at:  https://www.elastic.co/enterprise-search

Going with a simple installation of the latest as shared at:
https://medium.com/@davis.angwenyi/how-to-install-elastic-search-using-helm-into-kubernetes-de1fb1011076


Get helm and create namespace
```bash
helm repo add elastic https://helm.elastic.co  
helm search repo elastic 

kubectl create namespace elasticsearch
```

Created values file and made minor changes (see the above website for details)
```bash
helm show values elastic/filebeat > filebeat_values.yml 
helm show values elastic/logstash > logstash_values.yml 
helm show values elastic/elasticsearch > elasticsearch_values.yml 
helm show values elastic/kibana > kibana_values.yml
helm show values elastic/apm-server > apm-server_values.yaml
```

Then just simple installs in this order:
```bash
helm install elasticsearch elastic/elasticsearch -f elasticsearch_values.yml -n elasticsearch
helm install logstash elastic/logstash -f logstash_values.yml -n elasticsearch
helm install filebeat elastic/filebeat -f filebeat_values.yml -n elasticsearch
helm install kibana elastic/kibana -f kibana_values.yml -n elasticsearch  
helm install apm-server elastic/apm-server -f apm-server_values.yaml -n elasticsearch
```

Retrieve the elastic user's password and service account token
```bash
k get secrets --namespace=elasticsearch elasticsearch-master-credentials -ojsonpath='{.data.password}' | base64 -d
k get secrets --namespace=elasticsearch kibana-kibana-es-token -ojsonpath='{.data.token}' | base64 -d
```


If full cleaneup is needed
```bash
helm uninstall apm-server -n elasticsearch
helm uninstall filebeat -n elasticsearch
helm uninstall logstash -n elasticsearch
helm uninstall kibana -n elasticsearch
helm uninstall elasticsearch -n elasticsearch               
kubectl delete pvc -n elasticsearch --all
kubectl delete namespace elasticsearch
```



{
  printf '%s\n' '{"metadata":{"service":{"name":"test-service","agent":{"name":"custom","version":"1.0"}}}}'
  printf '%s\n' '{"transaction":{"id":"abc123","trace_id":"0123456789abcdef0123456789abcdef","type":"custom","duration":123.4,"name":"GET /hello","timestamp":1746186934000,"result":"success","span_count":{"started":0}}}'
} | curl -v http://localhost:8200/intake/v2/events \
     -H "Content-Type: application/x-ndjson" \
     -H "Authorization: Bearer 4a8e9f3d2b7c6e5f4a8e9f3d2b7c6e5f" \
     --data-binary @-

{
  printf '%s\n' '{"metadata":{"service":{"name":"test-service","agent":{"name":"custom","version":"1.0"}}}}'
  printf '%s\n' '{"transaction":{"id":"abc123","trace_id":"0123456789abcdef0123456789abcdef","type":"custom","duration":123.4,"name":"GET /hello","timestamp":1746186934000,"result":"success","span_count":{"started":0}}}'
} | curl -v http://localhost:8200/intake/v2/events \
     -H "Content-Type: application/x-ndjson" \
     --data-binary @-


    helm install apm-server elastic/apm-server \
      --set serviceAccount.create=true, \
      --set elasticsearchRef.name=<your-elasticsearch-name>
      --namespace elasticsearch     


  kubectl exec -n elasticsearch -it apm-server-apm-server-dc98ffb85-xfd4m -- curl -H "Authorization: Bearer 4a8e9f3d2b7c6e5f4a8e9f3d2b7c6e5f" http://apm-server-apm-server.elasticsearch.svc.cluster.local:8200