# home-lab: pre-setup
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
    sudo nmcli connection modify "eno1" ipv4.method manual ipv4.addresses 192.168.86.200/24 ipv4.gateway 192.168.86.1 ipv4.dns 8.8.8.8,8.8.4.4
    sudo nmcli connection modify "eno1" ipv6.method disabled
    sudo nmcli connection down "eno1"
    sudo nmcli connection up "eno1"



    nmcli connection show
    sudo nmcli connection modify "eno1" ipv4.method manual ipv4.addresses 192.168.86.202/24 ipv4.gateway 192.168.86.1 ipv4.dns 8.8.8.8,8.8.4.4
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

    192.168.86.200  8s-controller
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


Make sure files have the proper permissions

    chmod 600 ~/.ssh/id_rsa
    chmod 644 ~/.ssh/id_rsa.pub
    chmod 644 ~/.ssh/config

</li>
<li>Set alias for kubectl

    echo "alias k='kubectl'" >> ~/.bashrc
    source ~/.bashrc
</li>
</ol>
</li>
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
</li>
<li>Start up the dashboard

    kubectl proxy
</li>

Note: This may be redone once I get GitOps fully running
</ol>
<li>Install Flux to start GitOps Management
<ol>
<li>Install Flux CLI on the conroller:


Note: Having compatibility issues with latest Flux and CentOS Kubernetes which is v1.28.15 as the latest, so I want to force an install of v2.4.0


    curl -LO https://github.com/fluxcd/flux2/releases/download/v2.4.0/flux_2.4.0_linux_amd64.tar.gz
    tar -xzf flux_2.4.0_linux_amd64.tar.gz
    sudo mv flux /usr/local/bin/

</li>


<li>Bootstrap Flux

    flux bootstrap github \
    --owner=jaysuzi5 \
    --repository=home-lab \
    --branch=main \
    --path=cluster \
    --personal


</li>
<li>Check Flux

    flux check

    kubectl get pods -n flux-system
</li>

<li>Create my first application which will be redis.  See yamls under clsuter/apps/redis</li>
</ol>
</li>
</ol>