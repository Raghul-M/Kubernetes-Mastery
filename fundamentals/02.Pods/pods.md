# Kubernetes Pods

A Pod is the smallest deployable unit in Kubernetes. It represents a single instance of a running process in your cluster.

## 📌 What is a Pod?

- **Container Wrapper**: A Pod is a wrapper for containers. It defines how to run one or more containers together.
- **Shared Resources**: Multiple containers in a Pod share network and storage resources.
- **Single IP**: The Pod gets a cluster IP address to access applications inside containers, not individual container IPs.
- **YAML Manifest**: Pod definitions are stored in YAML files (like `podspec.yaml`) known as manifests.

## Pod Creation Methods

### Imperative Approach
Direct commands to create resources:

```bash
kubectl run nginx-pod --image=nginx:latest
```

### Declarative Approach
Uses YAML or JSON configuration files to create and manage resources:

```bash
kubectl create -f podspec.yaml
kubectl apply -f podspec.yaml
```

## Basic Pod Operations

### Create a Pod

```bash
kubectl run nginx-pod --image=nginx:latest
```

### List Pods

```bash
kubectl get pods
kubectl get pods -o wide
kubectl get pods nginx-pod --show-labels
```

### Get Pod Information

```bash
kubectl explain pod
kubectl describe pod nginx-pod
```

### Delete a Pod

```bash
kubectl delete pod nginx
```

### Edit a Pod

```bash
kubectl edit pod nginx-pod
```

### Execute Commands in Pod

```bash
kubectl exec -it nginx-pod -- sh
```

## YAML Examples

### Pod with Labels (podspec.yaml)

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    app: myapp-demo
    type: server
spec:
  containers:
  - name: nginx-server
    image: nginx:1.14.2
    ports:
    - containerPort: 80
```

### Redis Pod (podredis.yaml)

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    app: test
  name: redis
spec:
  containers:
  - image: redis
    name: redis
```

## Generating YAML Manifests

Generate YAML without creating the resource:

```bash
kubectl run nginx --image=nginx --dry-run=client -o yaml
kubectl run nginx --image=nginx --dry-run=client -o yaml > pod.yaml
```

### Generated Pod YAML (pod.yaml)

This is the YAML file generated using the dry-run command above:

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx
  name: nginx
spec:
  containers:
  - image: nginx
    name: nginx
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```

## Metadata and Labels

In the metadata section, you can add any key-value pairs as labels:

```yaml
metadata:
  name: my-pod
  labels:
    app: myapp
    version: v1.0
    environment: production
```

## Common Issues and Troubleshooting

### Image Pull Backoff Error

This error occurs when Kubernetes cannot pull the container image. Common causes:
- Incorrect image name or tag
- Private registry authentication issues
- Network connectivity problems

**Troubleshooting steps:**

```bash
kubectl describe pod nginx-pod
```

Look for events section to identify the specific error.

## Best Practices

1. **Use Declarative Approach**: Prefer YAML manifests over imperative commands for production
2. **Add Meaningful Labels**: Use consistent labeling strategy for better resource management
3. **Resource Limits**: Always specify resource requests and limits
4. **Health Checks**: Implement readiness and liveness probes
5. **Single Responsibility**: Keep one main container per Pod when possible

## Pod Lifecycle

- **Pending**: Pod has been accepted but containers are not running
- **Running**: Pod is bound to a node and all containers are running
- **Succeeded**: All containers terminated successfully
- **Failed**: All containers terminated, at least one failed
- **Unknown**: Pod state cannot be determined