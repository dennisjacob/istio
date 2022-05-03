#### Make egress calls to external


```
#Default is ALLOW_ANY. But it don't have any istio tracking or controlling possible from controlplane
root@ub-k8s-master:~# kubectl get istiooperator installed-state -n istio-system -o jsonpath='{.spec.meshConfig.outboundTrafficPolicy.mode}{"\n"}'
ALLOW_ANY

root@ub-k8s-master:~# kubectl exec -it http-app-dep1-697974ffdd-7fwpv -- curl -kI https://httpbin.org
HTTP/2 200
date: Mon, 02 May 2022 01:43:47 GMT
content-type: text/html; charset=utf-8
content-length: 9593
server: gunicorn/19.9.0
access-control-allow-origin: *
access-control-allow-credentials: true


root@ub-k8s-master:~# kubectl get istiooperator installed-state -n istio-system -o jsonpath='{.spec.meshConfig.outboundTrafficPolicy.mode}{"\n"}'
REGISTRY_ONLY
root@ub-k8s-master:~#




root@ub-k8s-master:~# kubectl exec -it http-app-dep1-697974ffdd-7fwpv  -- curl -kI https://www.google.com
curl: (35) OpenSSL SSL_connect: SSL_ERROR_SYSCALL in connection to www.google.com:443
command terminated with exit code 35
root@ub-k8s-master:~# kubectl exec -it http-app-dep1-697974ffdd-7fwpv  -- curl -kI https://httpbin.org
curl: (35) OpenSSL SSL_connect: SSL_ERROR_SYSCALL in connection to httpbin.org:443
command terminated with exit code 35


apiVersion: networking.istio.io/v1beta1
kind: ServiceEntry
metadata:
  name: se1
  namespace: my-ns
spec:
  hosts:
  - httpbin.org
  - edition.cnn.com
  - www.google.com
  ports:
  - name: HTTPS443
    number: 443
    protocol: HTTPS
  resolution: DNS
  location: MESH_EXTERNAL
---
apiVersion: networking.istio.io/v1beta1
kind: ServiceEntry
metadata:
  name: se2
  namespace: my-ns
spec:
  hosts:
  - httpbin.org
  - edition.cnn.com
  - www.google.com
  ports:
  - name: HTTP80
    number: 80
    protocol: HTTP
  resolution: DNS
  location: MESH_EXTERNAL



root@ub-k8s-master:~/istio# kubectl apply -f ../se.yaml
serviceentry.networking.istio.io/se1 configured
root@ub-k8s-master:~/istio# kubectl exec "$SOURCE_POD"  -- curl -sSI https://www.google.com | grep  "HTTP/"; kubectl exec "$SOURCE_POD"  -- curl -sI https://edition.cnn.com | grep "HTTP/"
HTTP/2 200
HTTP/2 200

root@ub-k8s-master:~/istio# kubectl exec "$SOURCE_POD"  -- curl -sSI http://httpbin.org
HTTP/1.1 200 OK
date: Mon, 02 May 2022 02:14:47 GMT
content-type: text/html; charset=utf-8
content-length: 9593
server: envoy
access-control-allow-origin: *
access-control-allow-credentials: true
x-envoy-upstream-service-time: 493



```

