```
kubectl apply -f https://raw.githubusercontent.com/cilium/cilium/v1.9/install/kubernetes/quick-install.yaml
kubectl apply -f https://raw.githubusercontent.com/cilium/cilium/v1.9/install/kubernetes/quick-hubble-install.yaml
```
./cilium-istioctl install -y

kubectl label namespace default istio-injection=enabled


```
kubectl port-forward -n kube-system svc/hubble-relay --address 0.0.0.0 --address :: 4245:80 &
```

```
kubectl port-forward -n kube-system svc/hubble-ui --address 0.0.0.0 --address :: 12000:80 &
```