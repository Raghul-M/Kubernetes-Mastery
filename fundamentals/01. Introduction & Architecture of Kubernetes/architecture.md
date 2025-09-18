
## **Kubernetes Architecture**

Kubernetes architecture consists of **Master (Control Plane)** and **Worker nodes (Data Plane)**.

* A **Node** is a virtual or physical machine.
* **Worker nodes** contain `kubelet`, an agent that manages Pods, which are the smallest deployable units where containers run.

---

#### **Master Node Components**

1. **API Server**: The front end of the Kubernetes control plane, handling requests from:

   * UI (Dashboard)
   * CLI (`kubectl`)
   * API calls
2. **Controller Manager**: Keeps track of the state of the cluster and ensures the desired state matches the current state. Includes:

   * Namespace Controller
   * Node Controller
   * Replication Controller
   * Endpoints Controller
   * Service Account & Token Controller etc..
3. **Scheduler**: Decides which node a Pod should run on based on resource availability and constraints.
4. **etcd**: A key-value store that holds **all cluster data**, including configuration, state, and metadata.
5. **Cloud Controller Manager**: Integrates Kubernetes with cloud provider APIs for resources like load balancers or storage.

---



#### **Worker Node (Data Plane) Components**

1. **kubelet**: Handles creation, monitoring, and health checks of Pods on the node. Receives instructions from the master node to ensure Pods are running correctly and reports back status.
2. **Container runtime**: Required to run containers. Examples: `containerd`, `cri-o`, `Docker`.
3. **kube-proxy**: Handles networking for the node. Responsible for:

   * Allocating IP addresses
   * Load balancing between Pods
   * Implementing network rules using IP tables

---

#### **Kubernetes Request Flow (User → Cluster → Response)**

1. **User sends a request** using `kubectl` or Dashboard.
2. **API Server** receives the request and validates it.
3. The desired state (e.g., create a Pod) is **written to `etcd`**.
4. **Controller Manager** detects the new desired state and instructs the **Scheduler** to assign the Pod to a suitable node.
5. **kubelet** on the chosen worker node receives instructions to create the Pod.
6. **Container runtime** launches the container(s) inside the Pod.
7. **kube-proxy** configures networking for the Pod.
8. Status is reported back to the **API Server**, which updates `etcd` and finally **acknowledges the user** that the request was successful.

---

