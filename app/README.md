Criando service account
```
kubectl apply -f - <<EOF
apiVersion: v1
kind: ServiceAccount
metadata:
  namespace: app
  name: review-app
EOF
```