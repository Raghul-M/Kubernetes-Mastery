
```
kind create cluster --image kindest/node:v1.33.1@sha256:050072256b9a903bd914c0b2866828150cb229cea0efe5892e2b644d5dd3b34f --name ragm-cluster1
```
```
brew install kind

kind --version
```
```
kind get clusters
```
```
kind create cluster --image kindest/node:v1.33.1@sha256:050072256b9a903bd914c0b2866828150cb229cea0efe5892e2b644d5dd3b34f --name ragm-cluster2 --config kind_Multinodeconfig.yaml
```
kubectl config current-context
kubectl config get-contexts
kubectl config use-context kind-ragm-cluster1