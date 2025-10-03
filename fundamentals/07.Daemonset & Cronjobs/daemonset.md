
# DaemonSet, Jobs & CronJobs

This guide covers three important Kubernetes workload controllers: **DaemonSet**, **Job**, and **CronJob**. Each serves a specific purpose in managing different types of workloads in your cluster.

<img width="1000" height="640" alt="image" src="https://github.com/user-attachments/assets/aa0ff116-9081-4846-a025-b18b4d014852" />


### 📌 **DaemonSet**

A DaemonSet ensures that **one copy of a Pod runs on every node** in the cluster (or on specific nodes using node selectors/tolerations).

#### Key Characteristics:
- **One pod per node**: Automatically schedules pods on all nodes
- **Node-specific workloads**: Perfect for system-level services
- **Automatic scaling**: When nodes are added/removed, pods are automatically created/deleted

#### Common Use Cases:
- **Logging agents** (Fluentd, Filebeat, Logstash)
- **Monitoring agents** (Prometheus Node Exporter, Datadog agent)
- **Networking components** (CNI plugins, kube-proxy)
- **Storage agents** (CSI drivers, Ceph agents)
- **Security agents** (Falco, security scanners)

#### Example:
If you have 5 worker nodes and a DaemonSet for a log collector, Kubernetes ensures **each node runs exactly one log collector pod automatically**.



### 📌 **Job**

A Job runs a **task once until it finishes successfully**. Think of it like running a script that has a start and an end.

#### Key Characteristics:
- **One-time execution**: Runs until completion or failure
- **No automatic restart**: Once finished, pods are not restarted
- **Parallel execution**: Can run multiple pods in parallel
- **Retry logic**: Can be configured to retry on failure

#### Common Use Cases:
- **Database migrations**
- **Batch email sending**
- **Data processing tasks**
- **Backup operations**
- **One-time maintenance tasks**

#### Example:
A Job that processes a batch of images or migrates data from one system to another.



### 📌 **CronJob**

A CronJob runs a Job **on a schedule (like a cron in Linux)**. It's essentially a scheduler that creates Jobs at specified times.

#### Key Characteristics:
- **Scheduled execution**: Uses cron syntax for scheduling
- **Automatic Job creation**: Creates new Job resources based on schedule
- **Job management**: Can limit concurrent jobs and handle failures
- **Timezone support**: Can specify timezone for scheduling

#### Common Use Cases:
- **Backup database every night at 12 AM**
- **Send reports every Monday morning**
- **Clean up old logs daily**
- **Generate analytics reports weekly**
- **Health checks every 5 minutes**

#### Example:
A CronJob that runs a backup script every day at 2 AM or sends a weekly report every Monday at 9 AM.

---

### DaemonSet Example
```yaml
# daemonset.yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: nginx-daemonset
spec:
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
        image: nginx:latest
        ports:
        - containerPort: 80
```

### Job Example
```yaml
# job.yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: hello-job
spec:
  template:
    spec:
      containers:
      - name: hello
        image: busybox
        command: ["echo", "Hello from the Job!"]
      restartPolicy: Never
```

### CronJob Example
```yaml
# cronjob.yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: hello-cron
spec:
  schedule: "*/1 * * * *"   # every 1 minute
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: hello
            image: busybox
            command: ["echo", "Hello from the CronJob!"]
          restartPolicy: Never
```

## 🔧 **Common kubectl Commands**

#### DaemonSet Commands
```bash
# Create a DaemonSet
kubectl apply -f daemonset.yaml

# List all DaemonSets
kubectl get daemonsets

# Describe a DaemonSet
kubectl describe daemonset nginx-daemonset

# Delete a DaemonSet
kubectl delete daemonset nginx-daemonset
```

#### Job Commands
```bash
# Create a Job
kubectl apply -f job.yaml

# List all Jobs
kubectl get jobs

# View Job logs
kubectl logs job/hello-job

# Delete a Job
kubectl delete job hello-job
```

#### CronJob Commands
```bash
# Create a CronJob
kubectl apply -f cronjob.yaml

# List all CronJobs
kubectl get cronjobs

# Suspend a CronJob
kubectl patch cronjob hello-cron -p '{"spec":{"suspend":true}}'

# Resume a CronJob
kubectl patch cronjob hello-cron -p '{"spec":{"suspend":false}}'

# Delete a CronJob
kubectl delete cronjob hello-cron
```

---

### 📊 **Comparison: ReplicaSet vs Deployment vs DaemonSet**

### **1. ReplicaSet**
- **Purpose**: Ensures a **specified number of replicas (copies) of a Pod are running** at all times
- **Scaling**: Based on desired replica count
- **Use Case**: You rarely use ReplicaSet directly; you use **Deployment**
- **Example**: If you specify 3 replicas → Kubernetes always tries to keep 3 running. If one pod dies, ReplicaSet creates a new one.

### **2. Deployment**
- **Purpose**: A higher-level controller that **manages ReplicaSets**
- **Features**: Adds **rolling updates, rollbacks, versioning** on top of ReplicaSet
- **Scaling**: Based on desired replica count
- **Example**: You want 3 replicas of your web app running. Later you want to update the app from v1 to v2 without downtime → Deployment does rolling updates.
- **Formula**: **Deployment = ReplicaSet + Easy Updates + Rollbacks**

### **3. DaemonSet**
- **Purpose**: Unlike ReplicaSet/Deployment (which control number of Pods), **DaemonSet makes sure 1 pod runs per node**
- **Scaling**: Based on number of nodes in the cluster
- **Example**: If you have 5 nodes and a DaemonSet for monitoring agent → 5 pods total (1 per node). If you add a new node → a new pod is automatically added.

---


## 🎯 **When to Use Each Controller**

#### ✅ **Use Deployment when:**
- You're running a **user application/service** that needs **scaling** and **updates**
- You care about **number of replicas**, not about "one per node"
- **Example cases:**
  - Web applications (Nginx, Flask, Node.js, etc.)
  - APIs / backends
  - Microservices
  - Frontends
  - Anything you want **load-balanced and scalable**

**👉 Key**: **Deployment = run N copies of my app, balance them across the cluster**

#### ✅ **Use DaemonSet when:**
- You're running a **system-level pod** that must exist on **every node** (or specific nodes)
- You care about **coverage** across nodes, not number of replicas
- **Example cases:**
  - Log collectors (Fluentd, Filebeat, Logstash)
  - Monitoring agents (Prometheus Node Exporter, Datadog agent)
  - Networking components (Calico, Cilium, kube-proxy, etc.)
  - Storage agents (Ceph, CSI drivers)

**👉 Key**: **DaemonSet = run one copy per node, automatically on all nodes**

#### ✅ **Use Job when:**
- You need to run a **one-time task** that has a clear start and end
- You want to process data, migrate databases, or perform maintenance
- You don't need the task to run continuously

#### ✅ **Use CronJob when:**
- You need to run Jobs **on a schedule**
- You want to automate recurring tasks like backups, reports, or cleanup
- You need time-based automation in your cluster

---

### 🔧 **Troubleshooting Common Issues**

#### DaemonSet Issues

**Problem**: DaemonSet pods not running on all nodes
```bash
# Check DaemonSet status
kubectl get daemonset
kubectl describe daemonset <daemonset-name>

# Check node labels and taints
kubectl get nodes --show-labels
kubectl describe node <node-name>
```

**Problem**: Pods stuck in Pending state
- Check node selectors and tolerations
- Verify resource requirements
- Check for node taints

#### Job Issues

**Problem**: Job not completing
```bash
# Check Job status
kubectl get jobs
kubectl describe job <job-name>

# View pod logs
kubectl logs job/<job-name>
```

**Problem**: Job failing repeatedly
- Check `restartPolicy` (should be `Never` or `OnFailure`)
- Verify image and command are correct
- Check resource limits

#### CronJob Issues

**Problem**: CronJob not creating Jobs
```bash
# Check CronJob status
kubectl get cronjobs
kubectl describe cronjob <cronjob-name>

# Check if suspended
kubectl get cronjob <cronjob-name> -o yaml | grep suspend
```

**Problem**: Too many concurrent Jobs
- Set `concurrencyPolicy: Forbid` to prevent overlapping
- Use `startingDeadlineSeconds` for timeout handling

### General Debugging Commands
```bash
# Get events for troubleshooting
kubectl get events --sort-by=.metadata.creationTimestamp

# Check pod status across all namespaces
kubectl get pods --all-namespaces

# View detailed pod information
kubectl describe pod <pod-name>
```

---







