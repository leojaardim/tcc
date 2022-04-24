```
 minikube start --network-plugin=cni --cni=false -p cenario3 --driver=virtualbox --cpus=4 --memory='8GB'
```

```
 minikube start --network-plugin=cni --cni=false -p cenario4 --driver=virtualbox --cpus=4 --memory='8GB'
```

```
minikube addons enable metrics-server
kubectl -n kube-system  apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml 
```
