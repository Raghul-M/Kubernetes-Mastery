# K3s Installation Guide

<div align="center">
  <img width="300" height="168" alt="K3s Logo" src="https://github.com/user-attachments/assets/70015f21-adb7-4f37-a0d1-666f13ece656" />
</div>

K3s is a lightweight, certified Kubernetes distribution designed for resource-constrained environments. It's perfect for edge computing, IoT devices, CI/CD pipelines, and development environments where you need a full Kubernetes cluster with minimal resource requirements.

## Prerequisites

Before installing K3s, ensure you have:
- A Linux system (Ubuntu, CentOS, RHEL, etc.)
- At least 512MB of RAM (1GB recommended)
- 1GB of free disk space
- Internet connection for downloading components
- Root or sudo access

## Installation Steps

### 1. Update System Packages

First, update your system packages to ensure you have the latest security patches:

```bash
sudo apt-get update
sudo apt-get upgrade
```

### 2. Install K3s Server (Single Node)

Install K3s server which will act as both control plane and worker node:

```bash
curl -sfL https://get.k3s.io | sh -
```

### 3. Verify Installation

Check if K3s is running properly:

```bash
sudo k3s kubectl get nodes
```

### 4. Get Kubeconfig

To use kubectl from your user account, copy the kubeconfig file:

```bash
sudo cp /etc/rancher/k3s/k3s.yaml ~/.kube/config
sudo chown $USER:$USER ~/.kube/config
```

### 5. Test Cluster Access

Verify that you can access the cluster:

```bash
kubectl get nodes
kubectl get pods --all-namespaces
```

## Multi-Node Setup (Optional)

### Agent Node Installation

To add worker nodes to your cluster, install K3s agent on additional machines:

```bash
curl -sfL https://get.k3s.io | K3S_URL=https://myserver:6443 K3S_TOKEN=mynodetoken sh -
```

Replace `myserver` with your server's IP and `mynodetoken` with the token from `/var/lib/rancher/k3s/server/node-token`.

## Configuration

### Custom Installation Options

Install K3s with custom options:

```bash
curl -sfL https://get.k3s.io | INSTALL_K3S_EXEC="--docker --write-kubeconfig ~/.kube/config" sh -
```

### Environment Variables

Set environment variables for custom configuration:

```bash
export INSTALL_K3S_EXEC="--docker"
export K3S_KUBECONFIG_MODE="644"
curl -sfL https://get.k3s.io | sh -
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
kubectl create deployment nginx --image=nginx
kubectl expose deployment nginx --type=NodePort --port=80
kubectl get services nginx
```

## Common Commands

### Service Management

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

### Cluster Information

```bash
# Get cluster info
kubectl cluster-info

# Get node information
kubectl get nodes -o wide

# Get all resources
kubectl get all --all-namespaces
```

### Logs and Debugging

```bash
# View K3s logs
sudo journalctl -u k3s -f

# Check K3s configuration
sudo cat /etc/rancher/k3s/k3s.yaml

# View containerd logs
sudo journalctl -u k3s-agent -f
```

## Uninstallation

### Remove K3s

To completely remove K3s from your system:

```bash
/usr/local/bin/k3s-uninstall.sh
```

### Clean Up Data

Remove all K3s data and configuration:

```bash
sudo rm -rf /var/lib/rancher/k3s
sudo rm -rf /etc/rancher/k3s
```

## Troubleshooting

### Common Issues

1. **Port Conflicts**: Ensure ports 6443, 10250, and 2379 are available
2. **Firewall Issues**: Configure firewall to allow K3s traffic
3. **Resource Constraints**: Ensure sufficient memory and disk space

### Check Logs

```bash
# K3s server logs
sudo journalctl -u k3s -f

# K3s agent logs
sudo journalctl -u k3s-agent -f

# Containerd logs
sudo journalctl -u containerd -f
```

### Reset Cluster

If you need to reset the cluster:

```bash
sudo k3s-killall.sh
sudo k3s-uninstall.sh
curl -sfL https://get.k3s.io | sh -
```

## Performance Optimization

### Resource Limits

Configure K3s with specific resource limits:

```bash
curl -sfL https://get.k3s.io | INSTALL_K3S_EXEC="--kubelet-arg=eviction-hard=imagefs.available<1%,nodefs.available<1%" sh -
```

### Storage Configuration

Use external storage for better performance:

```bash
curl -sfL https://get.k3s.io | INSTALL_K3S_EXEC="--data-dir=/opt/k3s" sh -
```

## Security Considerations

### Default Security

K3s runs with security best practices by default:
- Non-root containers
- AppArmor profiles
- Seccomp profiles
- Read-only root filesystem

### Custom Security Settings

Configure additional security options:

```bash
curl -sfL https://get.k3s.io | INSTALL_K3S_EXEC="--protect-kernel-defaults" sh -
```

## Resources

- [Official K3s Documentation](https://docs.k3s.io/)
- [K3s GitHub Repository](https://github.com/k3s-io/k3s)
- [Kubernetes Documentation](https://kubernetes.io/docs/)
- [Rancher Documentation](https://rancher.com/docs/)

## Support

If you encounter issues:

1. Check the [K3s troubleshooting guide](https://docs.k3s.io/installation/requirements)
2. Review the logs: `sudo journalctl -u k3s -f`
3. Verify system requirements and prerequisites
4. Check network connectivity and firewall settings
5. Visit the [K3s GitHub issues](https://github.com/k3s-io/k3s/issues) page
