# home-lab: pre-setup
This documents the machine setup prior to starting with the initial Ansible setup.  This includes installing linux and getting the machines on the local network.  
<hr/>

### Steps:

<ol>
<li>Create Linux bootable flash using balenaEtcher and latest image of Unbutu Server 24.04.2 LTS:  <a href=ubuntu.com/download/server>ubuntu.com/download/server</a></li>
<li>Install OpenSSH server from flash.  Skipping other options to keep it lightweight
    
        Machine Names:
           k8s-controller
           k8s-worker1
           k8s-worker2
</li>
<li>After install, update to make sure the machines have the latest updates:

       sudo apt update && sudo apt upgrade -y

and install nano

    sudo apt install nano

</li>
<li>Network Setup
    <ol>
    <li>Create Static IPs:  

Edit network config  

    sudo nano /etc/netplan/01-netcfg.yaml

Using IPs 200, 201, and 202

    network:
    version: 2
    renderer: networkd
    ethernets:
        eno1:
        dhcp4: no
        addresses:
            - 192.168.86.200/24
        routes:
            - to: default
            via: 192.168.86.1
        nameservers:
            addresses: [8.8.8.8, 8.8.4.4]

Important check if there are existing network configurations.  You can either edit those or create the above as new and remove the other, however having multiple can cause confusion on tracking down network issues.

    cd /etc/netplan
    ls
    
change permission and apply:
    
    sudo chmod 600 /etc/netplan/01-netcfg.yaml
    sudo netplan apply

</li>
<li>DNS: 

Ensure your router or /etc/hosts maps names to IPs for easier access:

    192.168.86.200  8s-controller
    192.168.86.201  k8s-worker1
    192.168.86.202  k8s-worker2

</li>
<li>SSH:

Check firewall and test SSH


    sudo ufw status
    sudo ufw allow ssh
</li>
</ol>
<li>Pre-Software Setup

<ol>
<li>Disable Swap:

    sudo swapoff -a
    sudo sed -i '/swap/d' /etc/fstab  # Remove swap entries

</li>Install Basic Tools:

    sudo apt install -y curl vim net-tools
<li>SSH Keys for Ansible: 

Generate an SSH key on the controller and copy it to the workers:

    ssh-keygen -t rsa -b 4096
    ssh-copy-id jay@k8s-worker1
    ssh-copy-id jay@k8s-worker2

Test passwordless SSH: 

    ssh jay@k8s-worker1
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
</ol>