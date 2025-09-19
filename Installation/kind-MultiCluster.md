
# Kind Multi-Cluster Setup

This guide demonstrates how to set up multiple Kubernetes clusters using Kind (Kubernetes in Docker) for local development and testing.

## 📌 Prerequisites

Before setting up multi-cluster environments, ensure you have the following installed:

- Docker
- Kind
- kubectl

## Installation

### Install Kind

```bash
brew install kind
```

Verify the installation:

```bash
kind --version
```

## Creating Multiple Clusters

### Single Node Cluster

Create a basic single-node cluster:

```bash
kind create cluster --image kindest/node:v1.33.1@sha256:050072256b9a903bd914c0b2866828150cb229cea0efe5892e2b644d5dd3b34f --name ragm-cluster1
```

### Multi-Node Cluster

Create a multi-node cluster using the configuration file:

```bash
kind create cluster --image kindest/node:v1.33.1@sha256:050072256b9a903bd914c0b2866828150cb229cea0efe5892e2b644d5dd3b34f --name ragm-cluster2 --config kind_Multinodeconfig.yaml
```

The `kind_Multinodeconfig.yaml` file defines a cluster with:
- 1 control-plane node
- 2 worker nodes

```yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
- role: worker
- role: worker
```

## Managing Multiple Clusters

### List All Clusters

```bash
kind get clusters
```

### Check Current Context

```bash
kubectl config current-context
```

### List All Contexts

```bash
kubectl config get-contexts
```

### Switch Between Clusters

To switch to a specific cluster:

```bash
kubectl config use-context kind-ragm-cluster1
```

## Example Workflow

1. **Create first cluster:**
   ```bash
   kind create cluster --name cluster1
   ```

2. **Create second cluster with custom config:**
   ```bash
   kind create cluster --name cluster2 --config kind_Multinodeconfig.yaml
   ```

3. **Verify clusters are running:**
   ```bash
   kind get clusters
   ```

4. **Switch to cluster1 and deploy an application:**
   ```bash
   kubectl config use-context kind-cluster1
   kubectl run nginx --image=nginx
   ```

5. **Switch to cluster2 and deploy a different application:**
   ```bash
   kubectl config use-context kind-cluster2
   kubectl run redis --image=redis
   ```

## Cleanup

To delete specific clusters:

```bash
kind delete cluster --name ragm-cluster1
kind delete cluster --name ragm-cluster2
```

To delete all clusters:

```bash
kind delete cluster --all
```