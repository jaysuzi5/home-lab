# action runnders
This runs argoCD commands through github workflows

Currently this is a manually deployed setup of a namespace and a deployment

## Setting Up GitHub Runners from within K8S cluser
See documentation at: https://github.com/actions/actions-runner-controller

### Prerequisites
certificate manager
```bash
kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.8.2/cert-manager.yaml
sudo install -m 555 argocd-darwin-amd64 /usr/local/bin/argocd
```



### Helm installation

```bash
helm repo add actions-runner-controller https://actions-runner-controller.github.io/actions-runner-controller
```

```bash
helm upgrade --install --namespace actions-runner-system --create-namespace\
  --set=authSecret.create=true\
  --set=authSecret.github_token="REPLACE_YOUR_TOKEN_HERE"\
  --wait actions-runner-controller actions-runner-controller/actions-runner-controller
```

### 
Install the specific deployment after the helm installation has been completed

```bash
kubectl apply -f deployment.yaml
```

```bash
cluster/
  ├── infrastructure/
  │   ├── actions-runner/
  │   │   ├── deployment.yaml
  │   │   
```

### Uninstall if necessary
```bash
helm uninstall actions-runner-controller -n actions-runner-system
```

