# Taints & Tolerations

## What are Taints and Tolerations?
<p align="center">
<img width="708" height="529" alt="image" src="https://github.com/user-attachments/assets/3a577b06-2f93-4a0e-84f5-3ea7a8174abc" />
</p>

**Taints** and **Tolerations** work together to ensure that pods are not scheduled onto inappropriate nodes. They provide a way to repel pods from nodes unless the pods have a matching toleration.

### Key Concepts:
- **Taint**: Applied to nodes to repel pods that don't have matching tolerations
- **Toleration**: Applied to pods to allow them to be scheduled on tainted nodes
- **Effect**: Defines what happens when a pod doesn't tolerate the taint

## Taints

### Taint Effects
There are three types of taint effects:

#### 1. NoSchedule
- **Behavior**: Prevents new pods from being scheduled on the node
- **Existing pods**: Not affected (remain running)
- **Use case**: Node maintenance, hardware-specific nodes

#### 2. PreferNoSchedule
- **Behavior**: Scheduler tries to avoid scheduling pods on the node
- **Guarantee**: No guarantee - pods may still be scheduled if no other nodes available
- **Use case**: Soft preference for node types

#### 3. NoExecute
- **Behavior**: Prevents new pods AND evicts existing pods that don't tolerate the taint
- **Existing pods**: Evicted if they don't have matching toleration
- **Use case**: Node failures, critical maintenance

### Managing Taints

#### Adding Taints
```bash
# Basic syntax
kubectl taint node <node-name> <key>=<value>:<effect>

# Examples
kubectl taint node worker-node-1 gpu=true:NoSchedule
kubectl taint node worker-node-2 disktype=ssd:PreferNoSchedule
kubectl taint node worker-node-3 maintenance=true:NoExecute
```

#### Removing Taints
```bash
# Remove taint by adding a dash (-) at the end
kubectl taint node <node-name> <key>=<value>:<effect>-

# Examples
kubectl taint node worker-node-1 gpu=true:NoSchedule-
kubectl taint node worker-node-2 disktype=ssd:PreferNoSchedule-
```

#### Viewing Taints
```bash
# View all nodes with their taints
kubectl describe nodes

# View specific node taints
kubectl describe node worker-node-1
```

## Tolerations

### Toleration Structure
Tolerations have the following structure:

```yaml
tolerations:
- key: "gpu"
  operator: "Equal"
  value: "true"
  effect: "NoSchedule"
```

### Toleration Fields

#### 1. key
- The taint key that this toleration matches
- Required field

#### 2. operator
- **Equal**: Key and value must match exactly
- **Exists**: Key must exist (value is ignored)

#### 3. value
- The taint value (only used when operator is "Equal")
- Optional when operator is "Exists"

#### 4. effect
- Must match the taint effect
- Can be: NoSchedule, PreferNoSchedule, NoExecute

### Toleration Examples

#### Example 1: Exact Match
```yaml
tolerations:
- key: "gpu"
  operator: "Equal"
  value: "true"
  effect: "NoSchedule"
```

#### Example 2: Key Exists
```yaml
tolerations:
- key: "maintenance"
  operator: "Exists"
  effect: "NoExecute"
```

#### Example 3: Multiple Tolerations
```yaml
tolerations:
- key: "gpu"
  operator: "Equal"
  value: "true"
  effect: "NoSchedule"
- key: "disktype"
  operator: "Equal"
  value: "ssd"
  effect: "PreferNoSchedule"
```

## Taints vs NodeSelector

### NodeSelector
- **Purpose**: Attract pods to specific nodes
- **Behavior**: Pods are scheduled on nodes with matching labels
- **Flexibility**: Pods can still be scheduled on other nodes if no matching nodes available

### Taints & Tolerations
- **Purpose**: Repel pods from specific nodes
- **Behavior**: Pods are prevented from being scheduled on tainted nodes
- **Flexibility**: Only pods with matching tolerations can be scheduled

### Comparison Table
| Feature | NodeSelector | Taints & Tolerations |
|---------|--------------|---------------------|
| Purpose | Attract pods | Repel pods |
| Default behavior | Schedule anywhere | Cannot schedule on tainted nodes |
| Matching requirement | Labels must match | Tolerations must match taints |
| Use case | Preferred nodes | Restricted nodes |

## Common Use Cases

### 1. GPU Nodes
```bash
# Taint GPU nodes
kubectl taint node gpu-node-1 gpu=true:NoSchedule
kubectl taint node gpu-node-2 gpu=true:NoSchedule

# Pods with GPU toleration can be scheduled
```

### 2. Node Maintenance
```bash
# Taint node for maintenance
kubectl taint node worker-node-1 maintenance=true:NoExecute

# Only pods with maintenance toleration remain
```

### 3. Dedicated Workloads
```bash
# Taint nodes for specific workloads
kubectl taint node database-node-1 database=true:NoSchedule
kubectl taint node web-node-1 web=true:NoSchedule
```

## Best Practices

1. **Use NoSchedule for hardware-specific nodes** (GPU, high-memory)
2. **Use PreferNoSchedule for soft preferences**
3. **Use NoExecute sparingly** - can disrupt running workloads
4. **Always test tolerations** before applying to production
5. **Document taint purposes** for team understanding
6. **Use meaningful taint keys** (gpu, disktype, zone, etc.)

## Commands Reference

```bash
# Add taint
kubectl taint node <node-name> <key>=<value>:<effect>

# Remove taint
kubectl taint node <node-name> <key>=<value>:<effect>-

# View taints
kubectl describe node <node-name>

# View all nodes with taints
kubectl get nodes -o custom-columns=NAME:.metadata.name,TAINTS:.spec.taints

# Label nodes (for nodeSelector)
kubectl label node <node-name> <key>=<value>

# Remove label
kubectl label node <node-name> <key>-
```
