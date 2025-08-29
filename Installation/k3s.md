# K3s Installation Guide
<br>
<div align="center">
  <img width="300" height="168" alt="K3s Logo" src="https://github.com/user-attachments/assets/b84bd623-f9d3-47b8-95ab-d59fb634d10c" />
</div>
<br><br>

K3s is a lightweight, certified Kubernetes distribution designed for resource-constrained environments. It's perfect for edge computing, IoT devices, CI/CD pipelines, and development environments where you need a full Kubernetes cluster with minimal resource requirements.

## Prerequisites

Before installing K3s, ensure you have:
- A Linux system (Ubuntu, CentOS, RHEL, etc.)
- At least 512MB of RAM (1GB recommended)
- 1GB of free disk space
- Internet connection for downloading components
- Root or sudo access

## Installation Steps

#### 1. Update System Packages

First, update your system packages to ensure you have the latest security patches:

```bash
sudo apt-get update
sudo apt-get upgrade
```

#### 2. Install K3s Server (Single Node)

Install K3s server which will act as both control plane and worker node:

```bash
curl -sfL https://get.k3s.io | sh -
```

#### 3. Verify Installation

Check if K3s is running properly:

```bash
k3s --version
```

#### 4. Get Kubeconfig

To use kubectl from your user account, copy the kubeconfig file:

```bash
sudo cp /etc/rancher/k3s/k3s.yaml ~/.kube/config
sudo chown $USER:$USER ~/.kube/config
```

#### 5. Test Cluster Access

Verify that you can access the cluster:

```bash
kubectl get nodes
kubectl get pods --all-namespaces
```


## Verification

### Check Cluster Status

Verify that your cluster is running:

```bash
kubectl cluster-info 
kubectl get nodes
```

### Check K3s Service

Monitor K3s service status:

```bash
sudo systemctl status k3s
```

### View Cluster Components

Check all system pods:

```bash
kubectl get pods --all-namespaces
```

## Test Deployment

Deploy a simple application to test your cluster:

```bash
kubectl create deployment nginx --image=nginx # Create a Deploymnt 

kubectl expose deployment nginx --type=NodePort --port=80 # Expose  a Deploymnt 

kubectl get services nginx
```

## Common Commands

#### Service Management

```bash
# Start K3s service
sudo systemctl start k3s

# Stop K3s service
sudo systemctl stop k3s

# Enable K3s to start on boot
sudo systemctl enable k3s

# Check service status
sudo systemctl status k3s
```


## Uninstallation

#### Remove K3s

To completely remove K3s from your system:

```bash
/usr/local/bin/k3s-uninstall.sh
```

#### Clean Up Data

Remove all K3s data and configuration:

```bash
sudo rm -rf /var/lib/rancher/k3s
sudo rm -rf /etc/rancher/k3s
```

## Troubleshooting

#### Common Issues

1. **Port Conflicts**: Ensure ports 6443, 10250, and 2379 are available
2. **Firewall Issues**: Configure firewall to allow K3s traffic
3. **Resource Constraints**: Ensure sufficient memory and disk space

#### Check Logs

```bash
# K3s server logs
sudo journalctl -u k3s -f

# K3s agent logs
sudo journalctl -u k3s-agent -f

# Containerd logs
sudo journalctl -u containerd -f
```


## Resources

- [Official K3s Documentation](https://docs.k3s.io/)
- [K3s GitHub Repository](https://github.com/k3s-io/k3s)
- [Kubernetes Documentation](https://kubernetes.io/docs/)
- [Rancher Documentation](https://rancher.com/docs/)


