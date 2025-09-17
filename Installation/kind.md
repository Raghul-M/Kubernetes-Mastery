# KIND (Kubernetes IN Docker) Installation Guide

<br>
<div align="center">
  <img width="289" height="174" alt="Kind logo" src="https://github.com/user-attachments/assets/fa5f3a3f-6ddc-4614-845d-51f79d5a6d5c" />
</div>
<br><br>


KIND (Kubernetes IN Docker) is a tool for running local Kubernetes clusters using Docker container "nodes". It's designed for testing Kubernetes itself and for local development and CI/CD workflows. KIND provides a fast and reliable way to create ephemeral Kubernetes clusters.

## Prerequisites

Before installing KIND, ensure you have:
- A Linux, macOS, or Windows system
- Docker installed and running
- At least 4GB of free memory
- 10GB of free disk space
- Internet connection for downloading components
- Go 1.11+ (for building from source, optional)

## Installation Steps

#### 1. Update System Packages

First, update your system packages to ensure you have the latest security patches:

```bash
sudo apt-get update
sudo apt-get upgrade
```

#### 2. Install Docker

Ensure Docker is installed and running:

```bash
sudo apt install docker.io
sudo systemctl start docker
sudo systemctl enable docker
```

Add your user to the docker group:

```bash
sudo usermod -aG docker $USER
newgrp docker
```

#### 3. Install KIND

Download and install KIND using the official installation script:

```bash
curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.20.0/kind-linux-amd64
chmod +x ./kind
sudo mv ./kind /usr/local/bin/kind
```

#### 4. Install kubectl

Install kubectl to interact with your KIND cluster:

```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
```

#### 5. Verify Installation

Check that both KIND and kubectl are properly installed:

```bash
kind --version
kubectl version --client
```

## Cluster Management

#### Create a Cluster

Create a basic single-node KIND cluster:

```bash
kind create cluster --name my-cluster
```

#### List Clusters

View all your KIND clusters:

```bash
kind get clusters
```

#### Delete a Cluster

Remove a specific cluster:

```bash
kind delete cluster --name my-cluster
```

## Verification

#### Check Cluster Status

Verify that your cluster is running:

```bash
kubectl cluster-info
kubectl get nodes
```

#### Check Cluster Context

Ensure kubectl is pointing to the correct cluster:

```bash
kubectl config current-context
kubectl config get-contexts
```

#### View Cluster Components

Check all system pods:

```bash
kubectl get pods --all-namespaces
```

## Test Deployment

Deploy a simple application to test your cluster:

```bash
kubectl create deployment nginx --image=nginx # Create a Deployment

kubectl expose deployment nginx --type=NodePort --port=80 # Expose a Deployment

kubectl get services nginx
```

## Configuration

#### Custom Cluster Configuration

Create a cluster with custom configuration using a YAML file:

```yaml
# kind-config.yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
  extraPortMappings:
  - containerPort: 80
    hostPort: 80
    protocol: TCP
- role: worker
```

Apply the configuration:

```bash
kind create cluster --name custom-cluster --config kind-config.yaml
```



## Common Commands

#### Cluster Management

```bash
# Create cluster with specific name
kind create cluster --name my-cluster

# Create cluster with specific Kubernetes version
kind create cluster --image kindest/node:v1.27.0

# Delete cluster
kind delete cluster --name my-cluster

# Delete all clusters
kind delete clusters --all
```

#### Cluster Information

```bash
# List all clusters
kind get clusters

# Get cluster info
kind get kubeconfig --name my-cluster

# Export cluster configuration
kind export kubeconfig --name my-cluster
```

#### Debugging

```bash
# View cluster logs
kind export logs --name my-cluster

# Check cluster status
kind get nodes --name my-cluster

# Execute commands in cluster nodes
kind exec --name my-cluster -- docker ps
```

## Troubleshooting

#### Common Issues

1. **Docker Not Running**: Ensure Docker daemon is started
2. **Port Conflicts**: Check for port conflicts on 6443, 80, 443
3. **Resource Constraints**: Ensure sufficient memory and disk space
4. **Permission Issues**: Verify Docker group membership

#### Check Logs

```bash
# View KIND logs
kind export logs --name my-cluster

# Check Docker logs
docker logs <container-id>

# View cluster events
kubectl get events --all-namespaces
```

#### Reset Cluster

If you need to reset the cluster:

```bash
kind delete cluster --name my-cluster
kind create cluster --name my-cluster
```







## Resources

- [Official KIND Documentation](https://kind.sigs.k8s.io/)
- [KIND GitHub Repository](https://github.com/kubernetes-sigs/kind)
- [Kubernetes Documentation](https://kubernetes.io/docs/)
- [Docker Documentation](https://docs.docker.com/)



