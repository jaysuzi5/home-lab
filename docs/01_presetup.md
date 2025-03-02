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
</li>
<li>Network Setup
    <ol>
    <li>Create Static IPs:  

Edit /etc/netplan/01-netcfg.yaml.  Using IPs 200, 201, and 202

    network:
    version: 2
    ethernets:
        eno1:
        addresses:
            - 192.168.86.200/24  
        gateway4: 192.168.1.1
        nameservers:
            addresses: [8.8.8.8, 8.8.4.4]
    
apply with:
    
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
    ssh-copy-id labadmin@k8s-wrk1
    ssh-copy-id labadmin@k8s-wrk2

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