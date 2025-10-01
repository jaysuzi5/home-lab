# home-lab: Initial Setup
This documents the machine setup prior and high level details detaisl for setting up Kubernetes.
<hr/>

### Steps:

<ol>
<li>Create Linux bootable flash using balenaEtcher and latest image of CentOS 10 Server</li>
<li>
        Machine Names:
           k8s-controller
           k8s-worker1
           k8s-worker2
</li>
<li>After install, update to make sure the machines have the latest updates:

       sudo dnf upgrade --refresh

</li>
<li>Network Setup
    <ol>
    <li>Create Static IPs:  

Edit network config and server name

    nmcli connection show
    sudo nmcli connection modify "eno1" ipv4.method manual ipv4.addresses 192.168.86.203/24 ipv4.gateway 192.168.86.1 ipv4.dns 8.8.8.8,8.8.4.4
    sudo nmcli connection modify "eno1" ipv6.method disabled
    sudo nmcli connection down "eno1"
    sudo nmcli connection up "eno1"


Rename the server if not done during the setup
    sudo hostnamectl
    sudo hostnamectl set-hostname k8s-controller
    sudo hostnamectl

Using IPs 200, 201, and 202

</li>
<li>DNS: 

Ensure your router or /etc/hosts maps names to IPs for easier access:

    192.168.86.200  k8s-controller
    192.168.86.201  k8s-worker1
    192.168.86.202  k8s-worker2

</li>
<li>SSH Keys for Access: 

Generate an SSH key on the controller and copy it to the workers:

First test with the password

    ssh jay@k8s-worker1
    ssh jay@k8s-worker2

Next generate the key and copy them:

    ssh-keygen -t rsa -b 4096
    ssh-copy-id jay@k8s-worker1
    ssh-copy-id jay@k8s-worker2

Finally, test passwordless SSH: 

    ssh jay@k8s-worker1
    ssh jay@k8s-worker2
</li>
<li>SSH Keys for Access from a developer machine (Optional): 
Note: I will be setting up the following on my macOS to make it easier to work with the Kubernetes cluster.

Generate an SSH key on the controller and copy it to the workers:

First test with the password
    ssh jay@k8s-controller
    ssh jay@k8s-worker1
    ssh jay@k8s-worker2

Next generate the key and copy them:

    ssh-keygen -t rsa -b 4096
    ssh-copy-id jay@k8s-controller
    ssh-copy-id jay@k8s-worker1
    ssh-copy-id jay@k8s-worker2


To simplfy more, we can update the ssh config file

    sudo nano ~/.ssh/config

Contents:

    Host controller
        HostName k8s-controller
        User jay
        Port 22

    Host worker1
        HostName k8s-worker1
        User jay
        Port 22

    Host worker2
        HostName k8s-worker2
        User jay
        Port 22

Finally, test passwordless SSH with the alias: 

    ssh controller
    ssh worker1
    ssh worker2

</li>
</ol>
</li>
<li>Create Backup Images
<ol>
<li>Download <a href="https://clonezilla.org/downloads.php">Clonezilla</a>
</li>
<li>Create a bootable USB drive following Clonezilla instructions
</li>
<li>Boot from the USB and complete these steps
</li>
<ol>
<li>Select Clonezilla Live</li>
<li>Select language and keyboard layout</li>
<li>Start -> Pick device-device for disk-to-disk cloning</li>
<li>Select Cloning Beginner Mode</li>
<li>disk_to_local_disk to clone one disk to another locally attached disk.</li>
<li>Select Source and Target Disks</li>
<li>Choose to clone the boot loader</li>
</ol>
</ol>
</li>
<li>Updating developer machine to manage cluster.  This will be done on my Mac Mini
<ol>
<li>Setup SSH to the nodes

    ssh-keygen -t rsa -b 4096
    ssh-copy-id jay@k8s-controller
    ssh-copy-id jay@k8s-worker1
    ssh-copy-id jay@k8s-worker2


Simplifying access:

    nano ~/.ssh/config

Copy the file to the config:

    Host controller
    HostName k8s-controller
    User jay
    Port 22

    Host worker1
        HostName k8s-worker1
        User jay
        Port 22

    Host wrk2
        HostName k8s-worker2
        User jay
        Port 22



# Connecting a Node:

1. System Preparation
```bash
# Update system
sudo dnf update -y

# Install basic dependencies
sudo dnf install -y curl wget vim git telnet net-tools

# Disable SELinux (temporarily for k8s) or set to permissive
sudo setenforce 0
sudo sed -i 's/^SELINUX=enforcing/SELINUX=permissive/' /etc/selinux/config

# Disable swap
sudo swapoff -a
sudo sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab

# Configure firewall (if enabled)
sudo systemctl stop firewalld
sudo systemctl disable firewalld
```

2. Configure System Parameters
```bash
# Load required kernel modules
sudo modprobe overlay
sudo modprobe br_netfilter

# Set sysctl parameters
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables  = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward                 = 1
EOF

sudo sysctl --system
```

3. Install Container Runtime (containerd)
```bash
# Install containerd
sudo dnf config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
sudo dnf install -y containerd.io

# Configure containerd
sudo mkdir -p /etc/containerd
containerd config default | sudo tee /etc/containerd/config.toml

# Set systemd cgroup driver
sudo sed -i 's/SystemdCgroup = false/SystemdCgroup = true/' /etc/containerd/config.toml

# Start containerd
sudo systemctl enable --now containerd
```


4. Install Kubernetes Components
```bash
# Add Kubernetes repo
cat <<EOF | sudo tee /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://pkgs.k8s.io/core:/stable:/v1.30/rpm/
enabled=1
gpgcheck=1
gpgkey=https://pkgs.k8s.io/core:/stable:/v1.30/rpm/repodata/repomd.xml.key
EOF

# Install kubelet, kubeadm, kubectl
sudo dnf install -y kubelet kubeadm kubectl --disableexcludes=kubernetes

# Enable kubelet
sudo systemctl enable --now kubelet
Join the Cluster
```

5. Get Join Command from Control Plane
On your existing control plane node:

```bash
# Create new token if needed
kubeadm token create --print-join-command

# Or check existing tokens
kubeadm token list
```

6. Join the Node
On your new node, run the join command from step 5:

```bash
# Example (use the actual command from your control plane)
sudo kubeadm join <CONTROL_PLANE_IP>:6443 \
  --token <TOKEN> \
  --discovery-token-ca-cert-hash sha256:<HASH>
Post-Join Verification
```

7. Verify Node Status
From your control plane:

```bash
# Check node status
kubectl get nodes

# Check system pods on new node
kubectl get pods -A -o wide | grep <new-node-name>

# Verify kube-proxy is running
kubectl get pods -n kube-system -l k8s-app=kube-proxy
```

8. Network Plugin Verification
Since you're using Flannel:

```bash
# Check flannel pod is running on new node
kubectl get pods -n kube-flannel -o wide

# Verify network connectivity
kubectl run test-nginx --image=nginx --restart=Never
kubectl get pods test-nginx -o wide
Optional: Node Labels and Taints
```

9. Configure Node (if needed)
```bash
# Add worker label if not present
kubectl label node <node-name> node-role.kubernetes.io/worker=worker

# Add any custom labels
kubectl label node <node-name> <key>=<value>
```

10. Add nfs tools

```bash
sudo yum install -y nfs-utils
sudo mount -t nfs 192.168.86.210:/volume2/k8s-data /mnt
sudo exportfs -ra
sudo systemctl restart nfs-server
```