
```
apiVersion: networking.istio.io/v1beta1
kind: Gateway
metadata:
  name: mygw1
  namespace: my-ns
spec:
  selector:
    istio: ingressgateway
  servers:
  - port:
      name: HTTP80
      number: 80
      protocol: HTTP
    hosts:
    - "*"
    tls:
      httpsRedirect: true
  - port:
      name: HTTPS443
      number: 443
      protocol: HTTPS
    hosts:
    - "*"
    tls:
      mode: SIMPLE
      credentialName: gw1-tls

---
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: myvs1
  namespace: my-ns
spec:
  hosts:
  - "*"
  gateways:
  - "my-ns/mygw1"
  http:
  - route:
    - destination:
        host: http-app-dep-svc
    match:
    - headers:
        token:
          exact: "PLAIN"
    headers:
      response:
        set:
          resp-header: "Plain resp"
  - route:
    - destination:
        host: https-app-dep-svc
    match:
    - headers:
        token:
          exact: "TLS"
    headers:
      response:
        set:
          resp-header: "TLS resp"


---
apiVersion: networking.istio.io/v1beta1
kind: DestinationRule
metadata:
  name: mydr1
  namespace: my-ns
spec:
  host: https-app-dep-svc
  trafficPolicy:
    tls:
      mode: SIMPLE


root@ub-k8s-master:~# curl -k https://secured.bethel.local --resolve secured.bethel.local:443:192.168.52.19  -H "token: PLAIN" -i
HTTP/2 200
date: Sat, 30 Apr 2022 12:10:59 GMT
server: istio-envoy
last-modified: Sat, 30 Apr 2022 07:05:45 GMT
etag: "4d-5ddd9c9b8e32f"
accept-ranges: bytes
content-length: 77
content-type: text/html
x-envoy-upstream-service-time: 0
resp-header: Plain resp

Server : http-app-dep2-6cd478bb5c-plltd on Deployment : app-http-app-dep2-v2
root@ub-k8s-master:~# curl -k https://secured.bethel.local --resolve secured.bethel.local:443:192.168.52.19  -H "token: TLS" -i
HTTP/2 200
date: Sat, 30 Apr 2022 12:11:01 GMT
server: istio-envoy
last-modified: Sat, 30 Apr 2022 07:06:00 GMT
etag: "4f-5ddd9ca9d4269"
accept-ranges: bytes
content-length: 79
content-type: text/html
x-envoy-upstream-service-time: 0
resp-header: TLS resp

Server : https-app-dep2-86b8686db7-7wh7c on Deployment : app-https-app-dep2-v2
root@ub-k8s-master:~#


```