# Multi-Container Pods

A Pod can contain multiple containers that work together. The most common patterns are Init Containers and Sidecar Containers.

## 📌 Container Patterns

### 1. Init Containers
- Run before the main application containers start
- Must complete successfully before main containers begin
- Used for setup, configuration, or dependency preparation

### 2. Sidecar Containers
- Run alongside the main application container
- Provide supporting functionality (logging, monitoring, caching)
- Share the same lifecycle as the main container

## Init Containers

Init containers run to completion before the main containers start. They are useful for:
- Setting up configuration files
- Downloading dependencies
- Waiting for external services
- Database migrations

### Init Container Example (init_containers.yaml)

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: init-demo
  labels:
    app: init-demo
spec:
  initContainers:
  - name: init-setup
    image: busybox:1.35
    command: ['sh', '-c']
    args:
    - |
      echo "Initializing application..."
      echo "Waiting for dependencies..."
      sleep 5
      echo "Init container completed successfully"
  containers:
  - name: main-app
    image: nginx:1.14.2
    ports:
    - containerPort: 80
```

## Sidecar Containers

Sidecar containers run alongside the main container and provide supporting functionality.

### Sidecar Container Example (sidecar_containers.yaml)

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: sidecar-demo
  labels:
    app: sidecar-demo
spec:
  containers:
  - name: main-app
    image: nginx:1.14.2
    ports:
    - containerPort: 80
  - name: log-processor
    image: busybox:1.35
    command: ['sh', '-c']
    args:
    - |
      echo "Log processor sidecar starting..."
      while true; do
        echo "$(date): Processing logs..."
        sleep 10
      done
  - name: cache-warmup
    image: redis:7-alpine
    command: ['sh', '-c']
    args:
    - |
      echo "Cache warmup sidecar starting..."
      redis-server --port 6379 &
      sleep 5
      redis-cli -p 6379 set "warmup" "completed"
      echo "Cache warmed up successfully"
      tail -f /dev/null
```

## Multi-Container Pod Commands

### Create and Manage Multi-Container Pods

```bash
# Create pod with init containers
kubectl apply -f init_containers.yaml

# Create pod with sidecar containers
kubectl apply -f sidecar_containers.yaml

# List pods
kubectl get pods

# Get pod details
kubectl describe pod init-demo
```

### Debug Multi-Container Pods

```bash
# Check pod status
kubectl get pod podname

# View environment variables
kubectl exec podname -- printenv

# Execute command in specific container
kubectl exec -it podname -c main-app -- sh

# Execute command in init container (if still running)
kubectl exec -it podname -c init-setup -- sh

# View logs from specific container
kubectl logs podname -c main-app

# View logs from init container
kubectl logs podname -c init-setup
```

### Container-Specific Operations

```bash
# List all containers in pod
kubectl get pod podname -o jsonpath='{.spec.containers[*].name}'

# Get container status
kubectl get pod podname -o jsonpath='{.status.containerStatuses[*].name}'

# Check init container status
kubectl get pod podname -o jsonpath='{.status.initContainerStatuses[*].name}'
```

## Shared Resources

### Network

All containers in a Pod share:
- Same IP address
- Same network namespace
- Same port space (containers can't use same port)

### Storage

Containers can share:
- File system (if needed)
- Temporary storage

## Use Cases

### Init Container Use Cases

1. **Database Migration**: Run database schema updates before app starts
2. **Configuration Download**: Download config files from external source
3. **Dependency Check**: Wait for external services to be ready
4. **Secret Generation**: Create certificates or keys

### Sidecar Container Use Cases

1. **Logging**: Collect and process application logs
2. **Monitoring**: Export metrics to monitoring systems
3. **Caching**: Provide local cache for the main application
4. **Security**: Run security scanners or proxies

## Best Practices

1. **Single Responsibility**: Each container should have a single, well-defined purpose
2. **Resource Limits**: Set appropriate resource requests and limits for each container
3. **Health Checks**: Implement readiness and liveness probes for main containers
4. **Logging**: Use structured logging and proper log levels
5. **Security**: Run containers with minimal required privileges
6. **Monitoring**: Monitor both main and sidecar containers
7. **Documentation**: Document the purpose and interaction of each container

## Troubleshooting

### Common Issues

1. **Init Container Failing**: Check init container logs and ensure it completes successfully
2. **Port Conflicts**: Ensure containers don't use the same port numbers
3. **Volume Mount Issues**: Verify volume mounts and permissions
4. **Resource Constraints**: Check if containers have sufficient resources

### Debug Commands

```bash
# Check pod events
kubectl describe pod podname

# View all container logs
kubectl logs podname --all-containers=true

# Check container resource usage
kubectl top pod podname --containers

# Debug specific container
kubectl debug pod/podname -c main-app --image=busybox:1.35
```
