# Kubernetes Deployments

A Deployment is a wrapper for Pods that enables auto-healing and auto-scaling capabilities. It provides declarative updates for Pods and ReplicaSets.

## 📌 What is a Deployment?

- **Auto-healing**: Automatically replaces failed or deleted Pods
- **Auto-scaling**: Can scale Pods up or down (with HPA - Horizontal Pod Autoscaler)
- **Rolling Updates**: Enables zero-downtime deployments
- **Rollback**: Provides ability to rollback to previous versions

## Deployment Hierarchy

```
Deployment → ReplicaSet → Pod
```

- **Deployment**: Contains the desired state and manages ReplicaSets
- **ReplicaSet**: Ensures the desired number of Pods are running
- **Pod**: The actual running container instances

## Basic Deployment Commands

### Create and Manage Deployments

```bash
# Create deployment from YAML
kubectl apply -f deployment.yaml

# List all deployments
kubectl get deploy

# Get deployment details
kubectl describe deployment nginx-deployment

# Delete deployment
kubectl delete deployment nginx-deployment
```

### ReplicaSet Commands

```bash
# List ReplicaSets
kubectl get rs

# Get ReplicaSet details
kubectl describe rs nginx-rs

# Scale ReplicaSet
kubectl scale --replicas=5 rs/nginx-rs

# Edit ReplicaSet live
kubectl edit rs/nginx-rs
```

### Pod Management

```bash
# List Pods with watch mode
kubectl get pods -w

# Get Pod details
kubectl describe pod <podname>

# View Pod logs
kubectl logs <podname>

# Delete a Pod (ReplicaSet will recreate it)
kubectl delete pod <podname>
```

## YAML Examples

### Deployment (deployment.yaml)

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx 
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
```

### ReplicaSet (replicaset.yaml)

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx-rs
  labels:
    app: demo
spec: 
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      name: nginx
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
        ports:
        - containerPort: 80
```

### ReplicationController (replicationcontroller.yaml)

```yaml
apiVersion: v1
kind: ReplicationController
metadata:
  name: nginx-rc
  labels:
    app: demo
spec: 
  replicas: 3
  template:
    metadata:
      name: nginx
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx  
        ports:
        - containerPort: 80
```

## ReplicationController vs ReplicaSet

### ReplicationController (RC) - Deprecated

- **Legacy**: Old way of ensuring specified number of Pods
- **Basic**: Limited functionality (basic self-healing only)
- **Selectors**: Only equality-based selectors (app=myapp)
- **Status**: Still works but deprecated

**Commands:**
```bash
# Check API version
kubectl explain rc

# Apply ReplicationController
kubectl apply -f replicationcontroller.yaml

# List ReplicationControllers
kubectl get rc

# Delete ReplicationController
kubectl delete rc/nginx
```

### ReplicaSet (RS) - Recommended

- **Modern**: Newer, improved version of ReplicationController
- **Advanced**: Supports set-based selectors
- **Flexible**: matchLabels and matchExpressions
- **Integration**: Used by Deployments under the hood

## Deployment Operations

### Update Deployment

```bash
# Update image in deployment
kubectl set image deploy/nginx-deployment nginx=nginx:1.16.1

# Check rollout status
kubectl rollout status deploy/nginx-deployment
```

### Rollout Management

```bash
# View rollout history
kubectl rollout history deploy/nginx-deployment

# Rollback to previous version
kubectl rollout undo deploy/nginx-deployment

# Rollback to specific revision
kubectl rollout undo deploy/nginx-deployment --to-revision=2
```

### Monitoring

```bash
# Get all resources
kubectl get all

# Watch Pods during deployment
kubectl get pods -w
```

## Rollout Strategies

A rollout is the process of deploying a new version of your application. Kubernetes provides several strategies:

| Strategy       | Downtime | Resource Usage    | Rollback Ease   | Use Case                    |
| -------------- | -------- | ----------------- | --------------- | --------------------------- |
| Rolling Update | None     | Minimal           | Moderate        | Default, safe               |
| Recreate       | Yes      | Minimal           | Easy            | Simple apps                 |
| Blue-Green     | None     | High (double env) | Very Easy       | Instant rollback            |
| Canary         | None     | Moderate          | Moderate        | Test new release gradually  |
| A/B Testing    | None     | Moderate          | Moderate        | Feature validation          |
| Shadow         | None     | High              | Monitoring-only | Validate under real traffic |
| Rolling+Surge  | None     | Moderate          | Moderate        | Production-critical updates |

## Troubleshooting Commands

### Debug Pod Issues

```bash
# Get detailed Pod information
kubectl describe pod <podname>

# View Pod logs
kubectl logs <podname>

# Execute commands in Pod
kubectl exec -it <podname> -- /bin/bash
```

### Debug Deployment Issues

```bash
# Check deployment status
kubectl describe deployment <deployment-name>

# View ReplicaSet details
kubectl describe rs <replicaset-name>

# Check events
kubectl get events --sort-by=.metadata.creationTimestamp
```

## Best Practices

1. **Use Deployments**: Always use Deployments instead of plain Pods for production
2. **Resource Limits**: Set resource requests and limits
3. **Health Checks**: Implement readiness and liveness probes
4. **Rolling Updates**: Use rolling update strategy for zero-downtime deployments
5. **Labels**: Use consistent labeling strategy
6. **Monitoring**: Monitor rollout status and Pod health
