## Setup K8s cluster with kubeadm
Set hostname to control plan and worker nodes

Update the hosts file with names and IPs of control plan server and worker nodes

---- ON ALL NODES ----
Enable Linux modules for containerd (it'll be loaded when the server start)
    cat << EOF | sodu tee /etc/modules-load.d/containerd.conf
    > overlay
    > br_netfilter
    EOF

Load manually the Linux modules
    sudo modprobe overlay
    sudo modprobe br_netfilter

Update the sysctl for k8s cri
    cat << EOF | sudo tee /etc/sysctl.d/99-kubernetes-cri.conf
    > net.bridge.bridge-nf-call-iptables  = 1
    > net.ipv4.ip_forward                 = 1
    > net.bridge.bridge-nf-call-ip6tables = 1
    EOF

Run command to apply the sysctl configurations
    sudo sysctl --system

Install containerd
    sudo apt-get update && sudo apt-get install -y containerd

Configure containerd
```
    sudo mkdir /etc/containerd
    sudo containerd config default | sudo tee /etc/containerd/config.toml
    sudo systemctl restart containerd
    sudo swapoff -a
    
    curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
    sudo apt-get update && sudo apt-get install -y apt-transport-https curl gpg
    sudo mkdir -p -m 755 /etc/apt/keyrings
    
    curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.29/deb/Release.key | sudo gpg - dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
    
    echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.29/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
    
    cat <<EOF | sudo tee /etc/apt/sources.list.d/kubernetes.list
    deb https://apt.kubernetes.io/ kubernetes-xenial main
    EOF
    
    sudo apt-get update $ sudo apt-get install -y kubelet kubeadm kubectl
    sudo apt-mark hold kubelet kubeadm kubectl
```
--- END ALL NODES ---

### Ref: 
https://medium.com/@subhampradhan966/how-to-install-kubernetes-cluster-kubeadm-setup-on-ubuntu-22-04-step-by-step-guide-dfcf33253f5f