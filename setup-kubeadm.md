Set hostname to control plan and worker nodes

Update the hosts file with names and IPs of control plan server and worker nodes

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