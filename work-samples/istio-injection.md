
#### Check if sidecar injection is supported

```
root@ub-k8s-master:~# kubectl api-versions | grep admission
admissionregistration.k8s.io/v1
root@ub-k8s-master:~# kubectl get mutatingwebhookconfigurations
NAME                            WEBHOOKS   AGE
istio-revision-tag-default      4          26d
istio-sidecar-injector          4          26d
istio-sidecar-injector-canary   2          58m
root@ub-k8s-master:~#
```
####  Manually inject the sidecar

```
istioctl kube-inject -f  test-deployment.yaml | kubectl apply -f -

```

#### Auto sidecar proxy injection

```
kubectl label namespace my-ns istio-injection=enabled  --overwrite=true
kubectl get namespace -L istio-injection


```
