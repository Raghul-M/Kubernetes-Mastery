Pod : It s an small unit in kubernetes 

- A definition of how to run a container. It is similar to arguments we pass to run the container but in Kubernetes it is in `podspec.yaml` file, known as a YAML manifest.
- A Pod can contain single or multiple containers. Multiple containers in a Pod share network and storage. The Pod gets a cluster IP to access the application inside the container; you don't get an IP for the container inside the Pod.
- A Pod is a wrapper for containers. In production, you can read the `pod.yaml` file to understand thousands of containers.

Imperative - kubectl run nginx 
Declarative - It uses json or yaml config file to craete a pod 

kubectl run nginx-pod --image=mginx:latest
kubectl get pods
kubectl explain pod to ckeck kind and version of api 

in the metadat label we can give any key value pair 

kubectl create -f podspec.yaml
kubectl delete pod nginx
kubectl apply -f podspec.yaml

common issue - Image Bull Backoff error 

kubectl describe pod nginx-pod
kubectl edit pod nginx-pod

kubectl exec -it nginx-pod -- sh
kubectl run nginx --image=nginx 
kubectl run nginx --image=nginx --dry-run=client -o yaml 
kubectl run nginx --image=nginx --dry-run=client -o yaml  > pod.yaml 