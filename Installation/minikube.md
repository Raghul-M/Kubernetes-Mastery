# Minikube Installation Guide

Minikube is a lightweight Kubernetes implementation that creates a local single-node cluster for development and testing purposes. It's perfect for learning Kubernetes concepts and developing applications locally.

## Prerequisites

Before installing Minikube, ensure you have:
- A Linux, macOS, or Windows system
- At least 2GB of free memory
- 20GB of free disk space
- Internet connection for downloading components


## Installation Steps

### 1. Update System Packages

First, update your system packages to ensure you have the latest security patches and dependencies:

```bash
sudo apt-get update
sudo apt-get upgrade
```

### 2. Download and Install Minikube

Download the latest version of Minikube for Linux:

```bash
curl -LO https://github.com/kubernetes/minikube/releases/latest/download/minikube-linux-amd64
```

Install Minikube to your system PATH:

```bash
sudo install minikube-linux-amd64 /usr/local/bin/minikube && rm minikube-linux-amd64
```

### 3. Install Docker

Minikube requires a container runtime. Docker is the most commonly used option:

```bash
sudo apt install docker.io
```

Verify Docker installation:

```bash
docker --help
```

### 4. Configure Docker Permissions

Add your user to the docker group to run Docker commands without sudo:

```bash
sudo usermod -aG docker $USER
```

Apply the new group membership:

```bash
newgrp docker
```

### 5. Start Docker Service

Check Docker service status:

```bash
sudo systemctl status docker
```

If Docker is not running, start it:

```bash
sudo systemctl start docker
sudo systemctl enable docker
```

### 6. Start Minikube Cluster

Start Minikube with Docker as the driver:

```bash
minikube start --driver=docker
```

## Troubleshooting

### Check Minikube Logs

If the cluster fails to start, check the logs for detailed error information:

```bash
minikube logs
```

### Available Drivers

View all available drivers for your system:

```bash
minikube drivers
```

### Profile Management

List all Minikube profiles:

```bash
minikube profile list
```
```
|----------|-----------|---------|--------------|------|---------|--------|-------|----------------|--------------------|
| Profile  | VM Driver | Runtime |      IP      | Port | Version | Status | Nodes | Active Profile | Active Kubecontext |
|----------|-----------|---------|--------------|------|---------|--------|-------|----------------|--------------------|
| minikube | docker    | docker  | 192.16.49.2 | 8443 | v1.33.1 | OK     |     1 | *              | *                  |
|----------|-----------|---------|--------------|------|---------|--------|-------|----------------|--------------------|
```



## Verification

### Check Cluster Status

Verify that your cluster is running:

```bash
minikube status
```

### Access Kubernetes Dashboard

Open the Kubernetes dashboard in your browser:

```bash
minikube dashboard
```

### Test Cluster

Deploy a simple application to test your cluster:

```bash
kubectl create deployment hello-minikube --image=k8s.gcr.io/echoserver:1.4
kubectl expose deployment hello-minikube --type=NodePort --port=8080
```

## Common Commands

### Start and Stop

```bash
# Start the cluster
minikube start

# Stop the cluster
minikube stop

# Delete the cluster
minikube delete
```

### Cluster Information

```bash
# Get cluster IP
minikube ip

# Get cluster status
minikube status

# Open cluster in browser
minikube service <service-name>
```

### Configuration

```bash
# Set memory limit
minikube start --memory=4096

# Set CPU limit
minikube start --cpus=2

# Set disk size
minikube start --disk-size=20g
```

## Next Steps

After successfully installing Minikube:

1. Install kubectl (Kubernetes command-line tool)
2. Learn basic kubectl commands
3. Deploy your first application
4. Explore Kubernetes concepts with hands-on practice

## Resources

- [Official Minikube Documentation](https://minikube.sigs.k8s.io/docs/)
- [Kubernetes Documentation](https://kubernetes.io/docs/)
- [Docker Documentation](https://docs.docker.com/)

## Support

If you encounter issues:

1. Check the [Minikube troubleshooting guide](https://minikube.sigs.k8s.io/docs/handbook/troubleshooting/)
2. Review the logs: `minikube logs`
3. Verify system requirements and prerequisites
4. Check Docker installation and permissions
