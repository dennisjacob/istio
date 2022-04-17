### Peer Authentication and mTLS

#### Install istio with values.global.proxy.privileged=true ( Only for troubleshooting )
```
root@ub-k8s-master:~# istioctl install -y  --set profile=demo  --set meshConfig.enableTracing=true --set values.global.proxy.privileged=true --set profile=demo
✔ Istio core installed
✔ Istiod installed
✔ Egress gateways installed
✔ Ingress gateways installed
✔ Installation complete                                                                                                                                                                                                                Making this installation the default for injection and validation.
Thank you for installing Istio 1.13.  Please take a few minutes to tell us about your install/upgrade experience!  https://forms.gle/pzWZpAvMVBecaQ9h9
```

#### Create is-inject-test-ns and my-ns with sidecar injected, and test-ns without sidecar injection
```
root@ub-k8s-master:~# kubectl create ns my-ns
namespace/my-ns created
root@ub-k8s-master:~# kubectl create ns is-inject-test-ns
namespace/is-inject-test-ns created
root@ub-k8s-master:~# kubectl create ns test-ns
namespace/test-ns created
root@ub-k8s-master:~# kubectl label namespace/my-ns namespace/is-inject-test-ns istio-injection=enabled
namespace/my-ns labeled
namespace/is-inject-test-ns labeled
root@ub-k8s-master:~# kubectl get ns -L istio-injection
NAME                STATUS   AGE     ISTIO-INJECTION
default             Active   91d     enabled
is-inject-test-ns   Active   111s    enabled
istio-system        Active   3m45s
kube-node-lease     Active   91d
kube-public         Active   91d
kube-system         Active   91d
metallb-system      Active   91d
mizu                Active   5d12h
my-ns               Active   2m5s    enabled
test-ns             Active   7s

```
