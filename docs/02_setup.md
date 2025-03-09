# home-lab: inital-setup
Once the machines have Linux and are up and running on the network, the following steps can be completed.  

Note: All steps will be done via ssh to terminal on k8s-controller except as noted.
<hr/>

### Steps:
<ol>
<li>Install Ansible</li>
<ol>
<li>Install Ansible on the controller

Note: I had issues installing ansible with dnf, so used pip instead

    sudo dnf install python3 python3-pip -y
    sudo pip3 install ansible
    ansible --version

</li>
<li>Create the inventory file:

    nano inventory.ini

with content

    [k8s_cluster]
    k8s-controller ansible_host=192.168.86.200
    k8s-worker1 ansible_host=192.168.86.201
    k8s-worker2 ansible_host=192.168.86.202

    [controller]
    k8s-controller

    [workers]
    k8s-worker1
    k8s-worker2

test the inventory

    ansible -i inventory.ini all --list-hosts

ping the nodes

    ansible -i inventory.ini all -m ping
</li>
<li>Create a playbook for Prerequisites

    k8s-install.yml

See playbooks directory for details

</li>
<li>Execute the Playbook

    ansible-playbook playbooks/k8s-install.yml


Validate on all nodes:

    docker --version
    kubelet --version
    kubeadm version
    kubectl version --client
</li>
<li>Initialize Kubernetes Cluster

    sudo kubeadm init --pod-network-cidr=10.244.0.0/16

Setup kubeconfig

    mkdir -p $HOME/.kube
    sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
    sudo chown $(id -u):$(id -g) $HOME/.kube/config


</li>
</ol>
<li>Install Kubernetes Dashboard</li>
<ol>
<li>Deployment

    kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml
</li>
<li>Set Admin account

    kubectl create serviceaccount dashboard-admin-sa
    kubectl create clusterrolebinding dashboard-admin-sa --clusterrole=cluster-admin --serviceaccount=default:dashboard-admin-sa
</li>
<li>Retrieve the token for the admin

    kubectl get secret $(kubectl get serviceaccount dashboard-admin-sa -o jsonpath="{.secrets[0].name}") -o jsonpath="{.data.token}" | base64 --decode
<li>

</ol>
<li></li>
<li></li>
</ol>