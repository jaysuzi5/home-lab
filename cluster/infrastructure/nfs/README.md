# NFS
Setting up an NFS client using helm to get access to my Synology NAS


Install nfs utils and set up mount on all worker nodes
```bash
sudo yum install -y nfs-utils
sudo mount -t nfs 192.168.86.210:/volume2/k8s-data /mnt
sudo exportfs -ra
sudo systemctl restart nfs-server
```


```bash
helm repo add nfs-subdir-external-provisioner https://kubernetes-sigs.github.io/nfs-subdir-external-provisioner/
helm repo update
helm install nfs-client nfs-subdir-external-provisioner/nfs-subdir-external-provisioner \
--set nfs.server="192.168.86.210" \
--set nfs.path="/volume2/k8s-data" -n default
```