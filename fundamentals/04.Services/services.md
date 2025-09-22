# Kubernetes Services
<p align="center">
<img width="915" height="678" alt="image" src="https://github.com/user-attachments/assets/730f48c9-7945-4d71-8f07-bf752334c9b2" />
</p>

A Kubernetes Service provides a stable network endpoint to access Pods reliably, abstracts Pod IPs, and supports different access patterns (internal, external, cloud load balancer).

## 📌 What is a Service?

A Service is an abstraction that provides a stable network endpoint for a set of Pods. It solves the problem of Pod IP addresses changing when Pods are recreated.

### Key Advantages:

1. **Load Balancing**: Distributes traffic across multiple Pods
2. **Service Discovery**: Provides stable DNS names for Pods
3. **External Access**: Exposes applications to the outside world

## How Services Work

- **Load Balancing**: Uses `kube-proxy` to perform load balancing
- **Label Selectors**: Uses labels and selectors to track and discover Pods
- **Stable IP**: Provides a stable IP address that doesn't change when Pods restart
- **DNS Resolution**: Services get DNS names for easy discovery

### Example Label Selector:

```yaml
selector:
  app: nginx
```

## Service Types

### 1. ClusterIP (Default)

Exposes the service internally within the Kubernetes cluster only.

**Characteristics:**
- Default service type
- Internal cluster access only
- Stable internal IP address
- Not accessible from outside the cluster

### 2. NodePort

Exposes the service on each Node's IP at a static port.

**Characteristics:**
- Port range: 30000-32767
- Accessible from outside the cluster
- Uses Node IP + NodePort
- Creates a ClusterIP service automatically

### 3. LoadBalancer

Exposes the service externally using a cloud provider's load balancer.

**Characteristics:**
- Cloud provider specific
- Not available in Minikube by default
- Creates NodePort and ClusterIP services automatically
- Provides external IP address

### 4. ExternalName

Maps the service to an external DNS name.

**Characteristics:**
- Maps to external service
- No selector required
- Returns CNAME record

## Port Configuration

### NodePort Service Ports

```yaml
ports:
  - port: 80        # Service's internal port
    targetPort: 80  # Pod's port
    nodePort: 30001 # Node port (30000-32767)
```

**Port Flow:**
```
External Request → NodePort → Service Port → Target Port (Pod)
```

### Kind Cluster Configuration

- **containerPort**: Port inside the Kind node container
- **hostPort**: Port on your local machine mapped to container port

## YAML Examples

### ClusterIP Service (clusterip.yaml)

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-clusterip
  labels:
    app: nginx
spec: 
  type: ClusterIP
  selector:
    app: nginx
  ports:
    - port: 80
      targetPort: 80
```

### NodePort Service (nodeport.yaml)

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-nodeport
  labels:
    app: nginx
spec: 
  type: NodePort
  selector:
    app: nginx
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30001
```

### LoadBalancer Service (loadbalancer.yaml)

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-loadbalancer
  labels:
    app: nginx
spec: 
  type: LoadBalancer
  selector:
    app: nginx
  ports:
    - port: 80
      targetPort: 80
```

### ExternalName Service (externalname.yaml)

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
  namespace: prod
spec:
  type: ExternalName
  externalName: my.api.example.com
```

## Basic Service Commands

### Create and Manage Services

```bash
# Create service from YAML
kubectl apply -f clusterip.yaml

# List all services
kubectl get svc

# Get service details
kubectl describe svc nginx-clusterip

# Delete service
kubectl delete svc nginx-clusterip
```

### Service Discovery

```bash
# Get service endpoints
kubectl get ep

# Get endpoints for specific service
kubectl get endpoints nginx-clusterip

# Test service connectivity from within cluster
kubectl exec pod-name -- curl nginx-clusterip:80
```

### Port Forwarding

```bash
# Forward local port to service
kubectl port-forward svc/nginx-clusterip 8080:80

# Forward local port to pod
kubectl port-forward pod/nginx-pod 8080:80
```

## Service Access Patterns

### Internal Access (ClusterIP)

```bash
# Access from within cluster
curl nginx-clusterip:80

# Access using DNS
curl nginx-clusterip.default.svc.cluster.local:80
```

### External Access (NodePort)

```bash
# Access using Node IP
curl <node-ip>:30001

# Get Node IP
kubectl get nodes -o wide
```

### External Access (LoadBalancer)

```bash
# Access using external IP
curl <external-ip>:80

# Get external IP
kubectl get svc nginx-loadbalancer
```

## Troubleshooting Commands

### Debug Service Issues

```bash
# Check service status
kubectl describe svc <service-name>

# Verify endpoints
kubectl get endpoints <service-name>

# Check service selector
kubectl get pods --selector app=nginx

# Test connectivity
kubectl exec -it <pod-name> -- curl <service-name>:<port>
```

### Common Issues

1. **No Endpoints**: Check if Pod labels match service selector
2. **Connection Refused**: Verify target port matches container port
3. **External Access**: Ensure NodePort is in valid range (30000-32767)

## Best Practices

1. **Use Labels**: Consistent labeling strategy for service discovery
2. **Resource Limits**: Set appropriate resource requests and limits
3. **Health Checks**: Implement readiness and liveness probes
4. **Service Types**: Choose appropriate service type for your use case
5. **DNS Names**: Use service DNS names instead of IP addresses
6. **Security**: Use NetworkPolicies to control traffic flow 
