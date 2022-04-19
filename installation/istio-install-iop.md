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

#### Uninstall the istio
```
root@ub-k8s-master:~# kubectl get iop -n istio-system
NAME     REVISION   STATUS    AGE
my-iop              HEALTHY   42m
root@ub-k8s-master:~# kubectl delete iop/my-iop -n istio-system
istiooperator.install.istio.io "my-iop" deleted
root@ub-k8s-master:~# istioctl operator remove
Removing Istio operator...
  Removed Deployment:istio-operator:istio-operator.
  Removed Service:istio-operator:istio-operator.
  Removed ServiceAccount:istio-operator:istio-operator.
  Removed ClusterRole::istio-operator.
  Removed ClusterRoleBinding::istio-operator.
✔ Removal complete

root@ub-k8s-master:~# istioctl manifest generate | kubectl delete -f -
root@ub-k8s-master:~# kubectl delete ns istio-system --grace-period=0 --force
namespace "istio-system" force deleted

```

#### In-place upgrade
```
root@ub-k8s-master:~# istioctl version
client version: 1.13.1
control plane version: 1.13.1
data plane version: none

root@ub-k8s-master:~# kubectl get pods --namespace istio-operator   -o=jsonpath='{range .items[*]}{.metadata.name}{":\t"}{range .spec.containers[*]}{.image}{", "}{end}{"\n"}{end}'
istio-operator-97b8dc444-kfcqn: docker.io/istio/operator:1.13.1,

root@ub-k8s-master:~# ls -dl istio*
lrwxrwxrwx 1 root root   12 Apr 19 15:39 istio -> istio-1.13.1
drwxr-x--- 6 root root 4096 Feb 18 22:20 istio-1.13.1
drwxr-x--- 6 root root 4096 Mar  8 21:41 istio-1.13.2

root@ub-k8s-master:~# ln -nfs istio-1.13.2 istio

root@ub-k8s-master:~# istioctl operator init --istioNamespace istio-system --operatorNamespace  istio-operator
Operator controller is already installed in istio-operator namespace.
Upgrading operator controller in namespace: istio-operator using image: docker.io/istio/operator:1.13.2
Operator controller will watch namespaces: istio-system
✔ Istio operator installed
✔ Installation complete


root@ub-k8s-master:~# kubectl get pods --namespace istio-operator   -o=jsonpath='{range .items[*]}{.metadata.name}{":\t"}{range .spec.containers[*]}{.image}{", "}{end}{"\n"}{end}'
istio-operator-67b876d65f-cps5p:        docker.io/istio/operator:1.13.2,


root@ub-k8s-master:~#  istioctl version
client version: 1.13.2
control plane version: 1.13.2
data plane version: 1.13.2 (2 proxies)

root@ub-k8s-master:~# kubectl get po -n istio-system
NAME                                    READY   STATUS    RESTARTS   AGE
istio-egressgateway-77568fc45c-kfgls    1/1     Running   0          39s
istio-ingressgateway-76b86f6b45-xc88m   1/1     Running   0          39s
istiod-587f7bd8ff-rsw9h                 1/1     Running   0          43s
root@ub-k8s-master:~#

```

#### Canary upgrade with iop

```

root@ub-k8s-master:~# ln -nfs istio-1.13.1 istio

root@ub-k8s-master:~# istioctl operator init --istioNamespace istio-system --operatorNamespace  istio-operator --revision 1-13-1
Installing operator controller in namespace: istio-operator using image: docker.io/istio/operator:1.13.1
Operator controller will watch namespaces: istio-system
2022-04-19T15:57:29.840079Z     info    proto: tag has too few fields: "-"
✔ Istio operator installed
✔ Installation complete

root@ub-k8s-master:~# kubectl get iop -n istio-system
NAME     REVISION   STATUS    AGE
my-iop              HEALTHY   17m

root@ub-k8s-master:~# kubectl get iop/my-iop  -n istio-system -o yaml >my-iop.yaml

root@ub-k8s-master:~# grep revision my-iop.yaml
  revision: 1-13-1
  
root@ub-k8s-master:~# kubectl apply -f my-iop.yaml

root@ub-k8s-master:~# kubectl get pod -n istio-system -l app=istiod
NAME                             READY   STATUS    RESTARTS   AGE
istiod-1-13-1-5bffb95b48-xjj84   1/1     Running   0          23s
istiod-587f7bd8ff-rsw9h          1/1     Running   0          17m

root@ub-k8s-master:~# kubectl get services -n istio-system -l app=istiod
NAME            TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                                 AGE
istiod          ClusterIP   10.104.181.182   <none>        15010/TCP,15012/TCP,443/TCP,15014/TCP   20m
istiod-1-13-1   ClusterIP   10.111.26.221    <none>        15010/TCP,15012/TCP,443/TCP,15014/TCP   52s

root@ub-k8s-master:~# kubectl get po
NAME                             READY   STATUS    RESTARTS   AGE
http-app-dep1-697974ffdd-92289   1/1     Running   0          8s
http-app-dep2-6cd478bb5c-g68xq   1/1     Running   0          8s

root@ub-k8s-master:~# kubectl label ns my-ns  istio.io/rev=1-13-1
namespace/my-ns labeled

root@ub-k8s-master:~# kubectl get ns/my-ns  -L 'istio.io/rev'
NAME    STATUS   AGE    REV
my-ns   Active   100s   1-13-1

root@ub-k8s-master:~# kubectl rollout restart deployment -n my-ns
deployment.apps/http-app-dep1 restarted
deployment.apps/http-app-dep2 restarted

root@ub-k8s-master:~# kubectl get po
NAME                             READY   STATUS        RESTARTS   AGE
http-app-dep1-6f65fd9847-t89fx   2/2     Running       0          8s
http-app-dep2-5cd9fb954b-d6hnm   2/2     Running       0          8s
root@ub-k8s-master:~#


```
