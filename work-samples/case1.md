### Gateway receives HTTP, and send to HTTP service

--- HTTP traffic ---> GW ---HTTP traffic --> services


```
root@ub-k8s-master:~# kubectl get svc/http-app-dep-svc
NAME               TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
http-app-dep-svc   ClusterIP   10.109.236.219   <none>        8080/TCP   56m


root@ub-k8s-master:~# cat case1.yaml
apiVersion: networking.istio.io/v1beta1
kind: Gateway
metadata:
  name: gw-case1
  namespace: my-ns
spec:
  selector:
    istio: ingressgateway
  servers:
  - hosts:
    - "secured.bethel.local"
    port:
      name: http
      number: 80
      protocol: HTTP
---
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: vs-case1
  namespace: my-ns
spec:
  hosts:
  - "secured.bethel.local"
  gateways:
  - gw-case1
  http:
  - match:
    - uri:
        prefix: "/"
    route:
    - destination:
        host: http-app-dep-svc
        port:
          number: 8080

root@ub-k8s-master:~# kubectl get svc/istio-ingressgateway -n istio-system  -ojsonpath='{.status.loadBalancer.ingress[0].ip} {"\n"}'
192.168.52.17

root@ub-k8s-master:~# istioctl proxy-config routes deploy/istio-ingressgateway.istio-system
NAME          DOMAINS                     MATCH                  VIRTUAL SERVICE
http.8080     secured.bethel.local        /*                     vs-case1.my-ns
http.8080     webapp.istioinaction.io     /*                     webapp-vs-from-gw.istioinaction
              *                           /stats/prometheus*
              *                           /healthz/ready*

root@ub-k8s-master:~# curl http://192.168.52.17 --resolve secured.bethel.local:80:192.168.52.17 -H "Host: secured.bethel.local" -i
HTTP/1.1 200 OK
date: Sat, 09 Apr 2022 07:26:14 GMT
server: istio-envoy
last-modified: Sat, 09 Apr 2022 06:23:33 GMT
etag: "4d-5dc32c0249f7c"
accept-ranges: bytes
content-length: 77
content-type: text/html
x-envoy-upstream-service-time: 0
Server : http-app-dep2-6cd478bb5c-ptfdf on Deployment : app-http-app-dep2-v2

root@ub-k8s-master:~# curl http://192.168.52.17 --resolve secured.bethel.local:80:192.168.52.17 -H "Host: secured.bethel.local" -i
HTTP/1.1 200 OK
date: Sat, 09 Apr 2022 07:26:32 GMT
server: istio-envoy
last-modified: Sat, 09 Apr 2022 06:23:33 GMT
etag: "4d-5dc32c0249f7c"
accept-ranges: bytes
content-length: 77
content-type: text/html
x-envoy-upstream-service-time: 0
Server : http-app-dep2-6cd478bb5c-ptfdf on Deployment : app-http-app-dep2-v2

```
