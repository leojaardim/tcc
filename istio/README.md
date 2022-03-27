For install istio on cluster

```
istioctl install --set profile=demo -y 
```

Set on namespace for instio work
```
kubectl label namespace default istio-injection=enabled

kubectl label namespace tcc istio-injection=enabled
kubectl label namespace app istio-injection=enabled
```
