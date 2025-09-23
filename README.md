
#  Kubernetes Mastery Roadmap

![image](https://github.com/user-attachments/assets/66bd6ad9-c519-4bfd-b3d8-539198b63d12)


### ✅ Phase 1: Core Kubernetes Concepts

- [X]  Kubernetes Introduction 
- [x]  Kubernetes  Architecture (control plane, worker nodes)
- [X]  Installation Methods (Kubeadm , kind , k3s)
- [x] kubectl Basics (`get`, `describe`, `logs`, `exec`)
- [x] Pods (multi-container pods, Init pods , Pod lifecycle)
- [x] Deployments (rolling updates, rollbacks, replicaset)
- [x] Services (ClusterIP, NodePort, LoadBalancer)
- [x] Namespaces
- [ ] DaemonSets , Jobs and CronJobs
- [ ] Labels and Selectors , Static Pods , Manual scheduling
- [ ] Taints and Tolerations
- [ ] Node Selectors, Affinity
- [ ] Resource Requests & Limits
- [ ] Horizontal Pod Autoscaler (HPA) vs Vertical Pod Autoscaler (VPA)
- [ ] Health Probes ( Liveness, Readiness, Startup Probes )
- [ ] ConfigMaps & Secrets
- [ ] SSL / TLS
- [ ] TLS Cerificate In Kubernetes cluster




### ✅ Phase 2: Configuration & Resource Management
- [ ] Volumes (emptyDir, hostPath)
- [ ] PersistentVolumes (PV) and PersistentVolumeClaims (PVC)
- [ ] StorageClasses





### ✅ Phase 3: Scheduling, Probes & Networking



- [ ] Ingress with NGINX Controller
- [ ] Network Policies



### ✅ Phase 4: Security

- [ ] RBAC (Roles, RoleBindings, ServiceAccounts)
- [ ] Pod Security Contexts (non-root users, readonly)
- [ ] Network Policies (namespace isolation)
- [ ] Admission Controllers (PodSecurity Standards)



### ✅ Phase 5: Helm, CRDs & Operators

- [ ] Helm (install, upgrade, rollback)
- [ ] Writing Custom Helm Charts
- [ ] CRDs (Custom Resource Definitions)
- [ ] Writing Operators (basic controller with Kopf or Operator SDK)



### ✅ Phase 6: Observability & Resource Management



- [ ] Metrics Server
- [ ] Prometheus + Grafana Monitoring
- [ ] Fluentd or Loki Logging



### ✅ Phase 7: CI/CD & GitOps

- [ ] GitHub Actions for K8s deployment
- [ ] ArgoCD
- [ ] Kustomize
- [ ] Skaffold or Tilt for local dev



### ✅ Phase 8: MLOps with Kubernetes

- [ ] Install and Use KServe
- [ ] Deploy ONNX/Torch model via KServe
- [ ] Use PVC or S3 for model storage
- [ ] GPU Scheduling in K8s
- [ ] Install and Run Kubeflow Pipelines



### ✅ Phase 9: Production-Grade Concepts

- [ ] Setup EKS/GKE/AKS (managed clusters)
- [ ] Cluster Autoscaler
- [ ] Velero for Backup & Restore
- [ ] Multi-tenant isolation (Namespaces, RBAC, Quotas)
- [ ] TLS Certificates with Cert-Manager

---

## 💻 Project Tracker

| Project | Description | Status |
|--------|-------------|--------|
| `flask-app + redis` | Deploy basic app with service, ingress | ⬜ Not Started |
| `mysql StatefulSet` | Use PVC, Headless service, init container | ⬜ Not Started |
| `model-serving-kserve` | Serve model using KServe | ⬜ Not Started |
| `github-actions-deploy` | Deploy app to K8s from CI/CD | ⬜ Not Started |
| `argocd-gitops` | GitOps pipeline using ArgoCD | ⬜ Not Started |
| `kubeflow-pipeline` | ML workflow using Kubeflow Pipelines | ⬜ Not Started |

---



## 🧠 Resources

- [Kubernetes Docs](https://kubernetes.io/docs/)
- [Kubernetes the Hard Way](https://github.com/kelseyhightower/kubernetes-the-hard-way)
- [Awesome Kubernetes](https://github.com/ramitsurana/awesome-kubernetes)

---

