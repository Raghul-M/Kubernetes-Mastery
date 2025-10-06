# Static Pods & Manual Scheduling

## Static Pods

### What are Static Pods?
Static pods are pods that are managed directly by the kubelet daemon on a specific node, without the API server observing them. They are defined by placing a pod definition file in a specific directory on the node.

### Key Characteristics:
- **Managed by kubelet**: The kubelet daemon is responsible for creating and managing static pods
- **No API server involvement**: Static pods are not managed by the Kubernetes API server
- **Node-specific**: Each static pod is tied to a specific node
- **Control plane components**: Most control plane components (etcd, kube-apiserver, kube-scheduler, kube-controller-manager) run as static pods

### Static Pod Directory
Static pods are defined in the `/etc/kubernetes/manifests/` directory on each node. The kubelet monitors this directory for pod definition files.

```bash
# View static pod definitions
ls /etc/kubernetes/manifests/
```

### Control Plane Components as Static Pods
The Kubernetes control plane components run as static pods:
- **kube-apiserver**: API server component
- **etcd**: Key-value store
- **kube-scheduler**: Pod scheduler
- **kube-controller-manager**: Controller manager

### Important Notes:
- If you remove the scheduler YAML from the manifests directory, the control plane will still work, but new pods won't be scheduled
- Static pods are automatically recreated by kubelet if they fail
- Changes to static pod definitions require restarting the kubelet service

## Manual Scheduling

### What is Manual Scheduling?
Manual scheduling allows you to bypass the default Kubernetes scheduler and directly specify which node a pod should run on. This is useful when you need precise control over pod placement.

### Methods of Manual Scheduling:

#### 1. Using `nodeName`
When you specify `nodeName` in the pod spec, Kubernetes will schedule the pod directly on that node without involving the scheduler.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: manual-pod
spec:
  nodeName: worker-node-1  # Directly specify the node
  containers:
  - name: nginx
    image: nginx
```

#### 2. Using `nodeSelector`
`nodeSelector` allows you to schedule pods on nodes that have specific labels.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: selector-pod
spec:
  nodeSelector:
    disktype: ssd  # Pod will only run on nodes with this label
  containers:
  - name: nginx
    image: nginx
```

### nodeName vs nodeSelector

#### nodeName
- **Direct node assignment**: Bypasses the scheduler completely
- **Exact node specification**: Pod runs on the exact node specified
- **No flexibility**: If the specified node is unavailable, pod remains pending
- **Use cases**: 
  - Testing and development
  - When you need a pod on a specific node
  - Debugging scenarios

```yaml
spec:
  nodeName: worker-node-1  # Must exist and be available
```

#### nodeSelector
- **Label-based selection**: Uses node labels to find suitable nodes
- **Flexible scheduling**: Can run on any node matching the labels
- **Better for production**: More resilient to node failures
- **Use cases**:
  - Production workloads
  - Hardware-specific requirements (SSD, GPU, etc.)
  - Zone or region-based scheduling

```yaml
spec:
  nodeSelector:
    disktype: ssd        # Any node with SSD storage
    zone: us-west-1a     # Any node in specific zone
```

#### Key Differences:
| Feature | nodeName | nodeSelector |
|---------|----------|--------------|
| Scheduler involvement | Bypassed | Still involved |
| Node flexibility | None | High |
| Production ready | No | Yes |
| Failure handling | Poor | Good |
| Use case | Testing/Debug | Production |

### Labels and Selectors

#### Labels
Labels are key-value pairs attached to Kubernetes objects (pods, nodes, services, etc.) that are used to organize and select subsets of objects.

```yaml
metadata:
  labels:
    app: frontend
    tier: web
    environment: production
```

#### Selectors
Selectors are used to filter and group resources based on their labels. There are two main ways to use selectors:

##### 1. In YAML Specifications
Use the `selector` field in pod specs to match labels:

```yaml
spec:
  selector:
    matchLabels:
      app: nginx
      tier: frontend
```

##### 2. Command Line Usage
Use selectors with kubectl commands to filter resources:

```bash
# Get pods with specific label
kubectl get pods --selector tier=frontend

# Get pods with multiple labels
kubectl get pods --selector app=frontend,tier=web

# Show all labels
kubectl get pods --show-labels

# Get nodes with specific label
kubectl get nodes --selector disktype=ssd
```

#### Node Labeling
To use nodeSelector, you need to label your nodes:

```bash
# Label a node
kubectl label nodes worker-node-1 disktype=ssd

# Remove a label
kubectl label nodes worker-node-1 disktype-

# View node labels
kubectl get nodes --show-labels
```

### Annotations
Annotations are used to store additional metadata about Kubernetes objects. Unlike labels, annotations are not used for selection or filtering.

```yaml
metadata:
  annotations:
    description: "This pod runs the main application"
    contact: "admin@company.com"
    version: "1.0.0"
```

### Best Practices:
1. **Use nodeSelector for simple node selection**
2. **Use nodeName only when you need absolute control**
3. **Label nodes with meaningful key-value pairs**
4. **Use annotations for metadata that doesn't affect scheduling**
5. **Avoid hardcoding node names in production environments**

### Commands Reference:
```bash
# View static pods
kubectl get pods --all-namespaces

# Check kubelet configuration
systemctl status kubelet

# View static pod directory
ls /etc/kubernetes/manifests/

# Label nodes
kubectl label nodes <node-name> <key>=<value>

# Get resources with selectors
kubectl get <resource> --selector <key>=<value>
```