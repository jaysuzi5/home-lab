





sudo yum install -y kubeadm-1.30.14-150500.1.1 --disableexcludes=kubernetes

# Upgrade node configuration
sudo kubeadm upgrade node

# Upgrade kubelet and kubectl
sudo yum install -y kubelet-1.30.14-150500.1.1 kubectl-1.30.14-150500.1.1 --disableexcludes=kubernetes

# Restart kubelet
sudo systemctl daemon-reload
sudo systemctl restart kubelet

# Exit the SSH session
exit