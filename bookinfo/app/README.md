kubectl apply -f samples/bookinfo/platform/kube/bookinfo.yaml

kubectl apply -f https://raw.githubusercontent.com/cilium/cilium/v1.10/examples/kubernetes-istio/bookinfo-gateway.yaml

```
export GATEWAY_URL=http://$(minikube -pcenario3 ip):$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="http2")].nodePort}') 
export PRODUCTPAGE_URL=${GATEWAY_URL}/productpage
echo ${PRODUCTPAGE_URL}
```

kubectl exec reviews-v1-545db77b95-8hfdr  -c istio-proxy -ti -- curl --connect-timeout 5 --fail http://details:9080/details/1

kubectl run carl --image=ubuntu:18.04 --command -- sleep 3600