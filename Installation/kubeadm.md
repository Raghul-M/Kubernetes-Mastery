# Kubeadm Installation Guide

<br>
<div align="center">
  <img width="300" height="168" alt="Kubeadm Logo" src="https://github.com/kubernetes-sigs/kubeadm/raw/main/logos/kubernetes-logo.png" />
</div>
<br><br>

This guide outlines the steps needed to set up a Kubernetes cluster using kubeadm.

## Prerequisites

- Ubuntu OS (Xenial or later)
- sudo privileges
- AWS Account
- Passion to Learn

## Master & Worker Node Setup

Run the following commands on both the master and worker nodes to prepare them for kubeadm.

### 1. Install kubectl

```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"

curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl.sha256"

echo "$(cat kubectl.sha256)  kubectl" | sha256sum --check

sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl

chmod +x kubectl
mkdir -p ~/.local/bin
mv ./kubectl ~/.local/bin/kubectl
# and then append (or prepend) ~/.local/bin to $PATH

kubectl version --client
```

### 2. Disable Swap

```bash
# disable swap
sudo swapoff -a
```

### 3. Configure Kernel Parameters

```bash
# Create the .conf file to load the modules at bootup
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF

sudo modprobe overlay
sudo modprobe br_netfilter

# sysctl params required by setup, params persist across reboots
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables  = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward                 = 1
EOF

# Apply sysctl params without reboot
sudo sysctl --system
```

### 4. Install CRI-O Runtime

```bash
sudo apt-get update -y
sudo apt-get install -y software-properties-common curl apt-transport-https ca-certificates gpg

sudo curl -fsSL https://pkgs.k8s.io/addons:/cri-o:/prerelease:/main/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/cri-o-apt-keyring.gpg
echo "deb [signed-by=/etc/apt/keyrings/cri-o-apt-keyring.gpg] https://pkgs.k8s.io/addons:/cri-o:/prerelease:/main/deb/ /" | sudo tee /etc/apt/sources.list.d/cri-o.list

sudo apt-get update -y
sudo apt-get install -y cri-o

sudo systemctl daemon-reload
sudo systemctl enable crio --now
sudo systemctl start crio.service

echo "CRI runtime installed successfully"
```

### 5. Install Kubernetes Components

```bash
# Add Kubernetes APT repository and install required packages
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.29/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.29/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list

sudo apt-get update -y
sudo apt-get install -y kubelet="1.29.0-*" kubectl="1.29.0-*" kubeadm="1.29.0-*"
sudo apt-get update -y
sudo apt-get install -y jq

sudo systemctl enable --now kubelet
sudo systemctl start kubelet
```

## Master Node Setup

### Initialize the Kubernetes Master Node

```bash
sudo kubeadm config images pull

sudo kubeadm init

mkdir -p "$HOME"/.kube
sudo cp -i /etc/kubernetes/admin.conf "$HOME"/.kube/config
sudo chown "$(id -u)":"$(id -g)" "$HOME"/.kube/config

# Network Plugin = calico
kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.26.0/manifests/calico.yaml
```

After successfully running, your Kubernetes control plane will be initialized successfully.

### Generate Join Token

Generate a token for worker nodes to join:

```bash
kubeadm token create --print-join-command
```

### Configure Security Group

Expose port 6443 in the Security group for the Worker to connect to Master Node.

## Worker Node Setup

### Join Worker Node to Cluster

Run the following commands on the worker node:

```bash
sudo kubeadm reset pre-flight checks
```

Paste the join command you got from the master node and append `--v=5` at the end. Make sure either you are working as sudo user or use `sudo` before the command.

## Verification

Verify if it is working as expected:

```bash
kubectl get nodes
```

## Common Commands

### Check Cluster Status

```bash
# Get cluster information
kubectl cluster-info

# Get nodes with details
kubectl get nodes -o wide

# Get all pods in all namespaces
kubectl get pods --all-namespaces

# Check system pods
kubectl get pods -n kube-system
```

### Service Management

```bash
# Check kubelet status
sudo systemctl status kubelet

# Check CRI-O status
sudo systemctl status crio

# Check kubeadm version
kubeadm version

# Check kubectl version
kubectl version --client
```

### Cluster Management

```bash
# Get join command
kubeadm token create --print-join-command

# View cluster configuration
kubectl config view

# Get component status
kubectl get componentstatuses
```

## Troubleshooting

### Common Issues

1. **Port Conflicts**: Ensure required ports are not in use
2. **Firewall Issues**: Configure firewall to allow Kubernetes traffic
3. **Swap Enabled**: Ensure swap is disabled on all nodes
4. **CRI-O Issues**: Verify CRI-O is running properly
5. **Network Issues**: Check Calico network plugin installation

### Check Logs

```bash
# Kubelet logs
sudo journalctl -u kubelet -f

# CRI-O logs
sudo journalctl -u crio -f

# Pod logs
kubectl logs <pod-name> -n <namespace>

# Cluster events
kubectl get events --all-namespaces
```

### Reset Cluster

If you need to reset the cluster:

```bash
# Reset kubeadm
sudo kubeadm reset

# Remove configuration files
sudo rm -rf ~/.kube
sudo rm -rf /etc/kubernetes
sudo rm -rf /var/lib/etcd
sudo rm -rf /etc/cni/net.d
```

## Test Deployment

Deploy a simple application to test your cluster:

```bash
# Create a deployment
kubectl create deployment nginx --image=nginx

# Expose the deployment
kubectl expose deployment nginx --type=NodePort --port=80

# Check services
kubectl get services nginx

# Scale the deployment
kubectl scale deployment nginx --replicas=3

# Check pods
kubectl get pods -l app=nginx
```

## Resources

- [Official Kubeadm Documentation](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/)
- [Kubeadm GitHub Repository](https://github.com/kubernetes/kubeadm)
- [CRI-O Documentation](https://cri-o.io/)
- [Calico Documentation](https://projectcalico.docs.tigera.io/)
- [Kubernetes Documentation](https://kubernetes.io/docs/)