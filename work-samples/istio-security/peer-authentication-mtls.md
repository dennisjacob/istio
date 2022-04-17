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

#### Deploy applications in all namespaces
```
root@ub-k8s-master:~# kns my-ns
Context "kubernetes-admin@kubernetes" modified.

root@ub-k8s-master:~# kubectl apply -f https-app-dep.yaml
deployment.apps/https-app-dep1 created
deployment.apps/https-app-dep2 created
service/https-app-dep-svc created

root@ub-k8s-master:~# kubectl get deploy
NAME             READY   UP-TO-DATE   AVAILABLE   AGE
http-app-dep1    1/1     1            1           41s
http-app-dep2    1/1     1            1           41s
https-app-dep1   1/1     1            1           34s
https-app-dep2   1/1     1            1           34s

root@ub-k8s-master:~# kubectl get svc
NAME                TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
http-app-dep-svc    ClusterIP   10.111.193.39    <none>        8080/TCP   74s
https-app-dep-svc   ClusterIP   10.104.131.167   <none>        8443/TCP   67s

root@ub-k8s-master:~# kns is-inject-test-ns
Context "kubernetes-admin@kubernetes" modified.

root@ub-k8s-master:~# kubectl run is-inject-test-ns-testapp --image=jacdenn/rhel7-httpd
pod/is-inject-test-ns-testapp created

root@ub-k8s-master:~# kubectl get po
NAME                        READY   STATUS    RESTARTS   AGE
is-inject-test-ns-testapp   2/2     Running   0          50s

root@ub-k8s-master:~# kns test-ns
Context "kubernetes-admin@kubernetes" modified.

root@ub-k8s-master:~# kubectl run test-ns-testapp --image=jacdenn/rhel7-httpd
pod/test-ns-testapp created

root@ub-k8s-master:~# kubectl get po
NAME              READY   STATUS    RESTARTS   AGE
test-ns-testapp   1/1     Running   0          32s




```
