# Kubernetes Namespaces
<p align="center">
<img width="1200" height="675" alt="image" src="https://github.com/user-attachments/assets/32f35af1-dfa0-478c-a248-7c09228753a7" />
</p>

Namespaces provide a way to divide cluster resources between multiple users, teams, or projects. They create logical separation and isolation of resources within a single Kubernetes cluster.

## 📌 What is a Namespace?

A Namespace is a virtual cluster within a Kubernetes cluster. It provides:
- **Resource Isolation**: Separate resources between different teams/projects
- **Scoping**: Limit resource visibility and access
- **DNS Resolution**: Enable service discovery within and across namespaces

## Default Namespaces

Kubernetes creates several default namespaces:

### 1. default
- Default namespace for resources when no namespace is specified
- Used for user-created resources

### 2. kube-system
- Contains system components and control plane resources
- Managed by Kubernetes itself
- Includes: API server, scheduler, controller manager, etcd

### 3. kube-node-lease
- Contains lease objects for node heartbeats
- Used for node health monitoring

### 4. kube-public
- Contains resources that should be readable by all users
- Used for cluster-wide configuration

### 5. local-path-storage
- Contains local storage provisioner
- Used for dynamic volume provisioning

## Cross-Namespace Communication

### DNS Resolution

When a Pod in one namespace needs to communicate with a Pod in another namespace, it must use the fully qualified domain name (FQDN):

```
<service-name>.<namespace-name>.svc.cluster.local
```

### DNS Configuration

The `/etc/resolv.conf` file is responsible for IP to DNS resolution internally in the cluster:

```bash
# Check DNS configuration
cat /etc/resolv.conf
```

### Example Cross-Namespace Communication

```bash
# Access service from different namespace
curl servicename.namespacename.svc.cluster.local
```

## Basic Namespace Commands

### List Namespaces

```bash
# List all namespaces
kubectl get ns

# List all namespaces (alternative)
kubectl get namespaces
```

### View Resources in Namespace

```bash
# Get all resources in specific namespace
kubectl get all --namespace=kube-system

# Get all resources in specific namespace (short form)
kubectl get all -n kube-system

# Get specific resource type in namespace
kubectl get pods -n kube-system
```

### Create and Delete Namespaces

```bash
# Create namespace
kubectl create ns demo

# Delete namespace
kubectl delete ns test-raghul

# Create namespace with YAML
kubectl apply -f namespace.yaml
```

### Deploy Resources in Namespace

```bash
# Create deployment in specific namespace
kubectl create deploy nginx-demo --image=nginx -n test-raghul

# Apply YAML to specific namespace
kubectl apply -f deployment.yaml -n test-raghul
```

### Access Resources in Namespace

```bash
# Execute command in pod within namespace
kubectl exec -it nginx-demo-98d9 -n test-raghul -- sh

# Port forward to service in namespace
kubectl port-forward svc/nginx-service 8080:80 -n test-raghul
```

## YAML Examples

### Create Namespace

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: production
  labels:
    name: production
    environment: prod
```

### Resource with Namespace

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  namespace: production
  labels:
    app: nginx
spec:
  containers:
  - name: nginx
    image: nginx:1.14.2
    ports:
    - containerPort: 80
```

## Namespace Management

### Set Default Namespace

```bash
# Set default namespace for current context
kubectl config set-context --current --namespace=production

# Verify current namespace
kubectl config view --minify --output 'jsonpath={..namespace}'
```

### Switch Between Namespaces

```bash
# List all contexts
kubectl config get-contexts

# Switch to different context/namespace
kubectl config use-context <context-name>
```

## Resource Quotas and Limits

### Resource Quota Example

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: compute-quota
  namespace: production
spec:
  hard:
    requests.cpu: "1"
    requests.memory: 1Gi
    limits.cpu: "2"
    limits.memory: 2Gi
    pods: "10"
```

### Limit Range Example

```yaml
apiVersion: v1
kind: LimitRange
metadata:
  name: mem-limit-range
  namespace: production
spec:
  limits:
  - default:
      memory: "512Mi"
    defaultRequest:
      memory: "256Mi"
    type: Container
```

## Service Discovery Across Namespaces

### Internal Service Access

```bash
# Access service in same namespace
curl nginx-service:80

# Access service in different namespace
curl nginx-service.production.svc.cluster.local:80
```

### DNS Resolution

```bash
# Test DNS resolution
nslookup nginx-service.production.svc.cluster.local

# Check service endpoints
kubectl get endpoints nginx-service -n production
```

## Troubleshooting Commands

### Debug Namespace Issues

```bash
# Check namespace status
kubectl describe ns production

# List all resources in namespace
kubectl get all -n production

# Check resource quotas
kubectl get resourcequota -n production

# Check limit ranges
kubectl get limitrange -n production
```

### Common Issues

1. **Resource Not Found**: Check if resource exists in correct namespace
2. **Permission Denied**: Verify RBAC permissions for namespace
3. **DNS Resolution**: Use FQDN for cross-namespace communication
4. **Resource Limits**: Check if namespace has resource quotas

## Best Practices

1. **Logical Separation**: Use namespaces to separate environments (dev, staging, prod)
2. **Team Isolation**: Create namespaces for different teams or projects
3. **Resource Management**: Set resource quotas and limits per namespace
4. **RBAC**: Implement proper role-based access control
5. **DNS Naming**: Use consistent naming conventions for services
6. **Monitoring**: Monitor resource usage per namespace
7. **Cleanup**: Regularly clean up unused namespaces and resources
