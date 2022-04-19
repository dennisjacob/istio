### Istion installation with iop(istio operator)

#### Install the Istio Operator
```
root@ub-k8s-master:~# istioctl operator init --istioNamespace istio-system --operatorNamespace  istio-operator
Installing operator controller in namespace: istio-operator using image: docker.io/istio/operator:1.13.2
Operator controller will watch namespaces: istio-system
✔ Istio operator installed
✔ Installation complete
root@ub-k8s-master:~#


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

#### Update the iop ( profile as example )
```
root@ub-k8s-master:~# kubectl get IstioOperator/my-iop  -n istio-system
NAME     REVISION   STATUS    AGE
my-iop              HEALTHY   3m9s

root@ub-k8s-master:~# kubectl get IstioOperator/my-iop  -n istio-system -oyaml  | sed 's/profile: demo/profile: default/g' | kubectl apply -f -
istiooperator.install.istio.io/my-iop configured

root@ub-k8s-master:~# kubectl get IstioOperator/my-iop  -n istio-system -ojsonpath='{.spec.profile}{"\n"}'
default


```
