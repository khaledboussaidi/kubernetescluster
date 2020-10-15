##  Kubernetes the Hard Way installation
-> For this installation, we recommend Ubuntu 18.04 image.

* To install Kubernetes, you have to diligently follow the 3 phases that come as part of the installation process:
I-Pre-requisites to install Kubernetes
II-Setting up Kubernetes environment
III-Installing Kubeadm, Kubelet, Kubectl
IV-Starting the Kubernetes cluster from master
V-Getting the nodes to join the cluster

* To install Kubernetes we need :
3 VMs master and 2 nodes that:
Master:
  2 GB RAM
  2 Cores of CPU
Every Node:
  1 GB RAM
  1 Core of CPU
  
  # I-Pre-requisites to install Kubernetes:
  -> The following steps have to be executed on both the master and node machines.
   I recommend to login as sudo user because the following set of commands need to be executed with sudo permissions. then update every machine and  turn off swap space:
   $ sudo su
   $ apt-get update
   $ swapoff -a
   and remove any matching reference found in /etc/fstab
   
   If you need to change host name:
   $ nano /etc/hostname
   and rename the master machine and your nodes machines.
   
   Update The Hosts File With IPs Of Master & Node:
   $ ifconfig  
   and copy the address which under enp0s8. then  go to the hosts file on both the master and nodes and add an entry specifying their respective IP addresses along with their names. This is used for referencing them in the cluster.
   $ nano /etc/hosts
   
   Setting Static IP Addresses:
   Next, we will make the IP addresses used above, static for the VMs. We can do that by modifying the network interfaces file. Run the following command to open the file:
   $ nano /etc/network/interfaces
    And enter the following lines in the file.
    auto enp0s8
    iface enp0s8 inet static
    address <IP-Address-Of-VM>
    
   Install OpenSSH-Server:
   $ apt-get install openssh-server
    
   Install Docker:
   $ apt-get install -y docker.io
    
   Next we have to install these 3 essential components for setting up Kubernetes environment: kubeadm, kubectl, and kubelet:
    
   $ apt-get update && apt-get install -y apt-transport-https curl
   $ curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
   $ cat <<EOF >/etc/apt/sources.list.d/kubernetes.list deb http://apt.kubernetes.io/ kubernetes-xenial main EOF
   $ apt-get update
    
   Install kubeadm, Kubelet And Kubectl: 
   $ apt-get install -y kubelet kubeadm kubectl 
    
   Updating Kubernetes Configuration:
   $ nano /etc/systemd/system/kubelet.service.d/10-kubeadm.conf
   and add the following line:
   Environment="cgroup-driver=systemd/cgroup-driver=cgroupfs"
    
  # Steps Only For Kubernetes Master VM
  start your Kubernetes cluster from the master’s machine. Run the following command:
  $ kubeadm init --apiserver-advertise-address=<ip-address-of-kmaster-vm> --pod-network-cidr=192.168.0.0/16
  
  # PS:
   You will get the below output. The commands marked as (1), execute them as a non-root user. This will enable you to use kubectl from the CLI
   The command marked as (2) should also be saved for future. This will be used to join nodes to your cluster
   
   To verify, if kubectl is working or not, run the following command:
   $ kubectl get nodes
   you will see the master.
   
   
   You will notice from the previous command, that all the pods are running except kube-dns. 
   For resolving this we will install a pod network. To install the CALICO pod network, run the following command:
   $ kubectl apply -f https://docs.projectcalico.org/v3.14/manifests/calico.yaml
   
   
   # Only Kubernetes Nodes VM
   $ kubeadm join --apiserver-advertise-address=<ip-address-of-the master> --pod-network-cidr=192.168.0.0/16 --token ***********


