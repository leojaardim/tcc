Install calicoctl
```
curl -L https://github.com/projectcalico/calico/releases/download/v3.22.1/calicoctl-linux-amd64 -o calicoctl
```

Change permission
```
chmod +x ./calicoctl
```

Move to /usr/local/bin
```
chmod +x ./calicoctl
```

Source: https://projectcalico.docs.tigera.io/maintenance/clis/calicoctl/install

###

Enabled calico on istio

```
calicoctl patch FelixConfiguration default --patch \
   '{"spec": {"policySyncPathPrefix": "/var/run/nodeagent"}}'

```

```
kubectl create -f - <<EOF
apiVersion: security.istio.io/v1beta1
kind: PeerAuthentication
metadata:
  name: default-strict-mode
  namespace: istio-system
spec:
  mtls:
    mode: STRICT
EOF
```

```
curl https://projectcalico.docs.tigera.io/manifests/alp/istio-inject-configmap-1.10.yaml -o istio-inject-configmap.yaml
kubectl patch configmap -n istio-system istio-sidecar-injector --patch "$(cat istio-inject-configmap.yaml)"
```

```
kubectl apply -f https://projectcalico.docs.tigera.io/manifests/alp/istio-app-layer-policy-envoy-v3.yaml
```

Source: https://projectcalico.docs.tigera.io/security/app-layer-policy