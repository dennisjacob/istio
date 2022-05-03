
```
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
---
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: vs1
  namespace: my-ns
spec:
  hosts:
  - httpbin.org
  http:
  - route:
    - destination:
        host: httpbin.org
    fault:
      delay:
        fixedDelay: 5s
        percentage:
          value: 100.0

root@ub-k8s-master:~/istio# kubectl apply -f ../se.yaml
serviceentry.networking.istio.io/se1 unchanged
serviceentry.networking.istio.io/se2 unchanged
virtualservice.networking.istio.io/vs1 unchanged
root@ub-k8s-master:~/istio# time kubectl exec "$SOURCE_POD"  -- curl -sSI http://httpbin.org
HTTP/1.1 200 OK
date: Mon, 02 May 2022 02:28:10 GMT
content-type: text/html; charset=utf-8
content-length: 9593
server: envoy
access-control-allow-origin: *
access-control-allow-credentials: true
x-envoy-upstream-service-time: 497


real    0m5.705s
user    0m0.037s
sys     0m0.027s


```