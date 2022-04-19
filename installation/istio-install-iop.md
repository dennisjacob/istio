### Istion installation with iop(istio operator)

#### Install the Operator
```
root@ub-k8s-master:~# istioctl operator init --watchedNamespaces istio-ns --dry-run
Installing operator controller in namespace: istio-operator using image: docker.io/istio/operator:1.13.2
Operator controller will watch namespaces: istio-ns
✔ Istio operator installed
✔ Installation complete

root@ub-k8s-master:~# istioctl operator init --watchedNamespaces istio-ns
Installing operator controller in namespace: istio-operator using image: docker.io/istio/operator:1.13.2
Operator controller will watch namespaces: istio-ns
✔ Istio operator installed
✔ Installation complete

```

#### Install Istio components
```

root@ub-k8s-master:~# cat istio-install.yaml
# https://istio.io/latest/docs/reference/config/istio.operator.v1alpha1/#IstioOperatorSpec
apiVersion: install.istio.io/v1alpha1
kind: IstioOperator
metadata:
  name: my-iop
  namespace: istio-ns
spec:
  profile: demo
  meshConfig:
    enableTracing: true
    accessLogFile: /dev/stdout
    enableEnvoyAccessLogService: true
  components:
    egressGateways:
    - name: istio-egressgateway
      enabled: true
    ingressGateways:
    - name: istio-ingressgateway
      enabled: true

root@ub-k8s-master:~# kubectl apply -f istio-install.yaml
istiooperator.install.istio.io/my-iop created

```
