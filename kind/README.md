Start cluster with kind
```
kind create cluster --config cluster.yaml
```
Install CNI Calico
```
kubectl apply -f https://projectcalico.docs.tigera.io/v3.22/manifests/calico.yaml
```
Ignore RFP
```
kubectl -n kube-system set env daemonset/calico-node FELIX_IGNORELOOSERPF=true
```