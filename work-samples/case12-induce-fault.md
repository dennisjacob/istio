#### Induce a fault 

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
      weight: 100
    fault:
      abort:
        httpStatus: 501
        percentage:
          value: 50.0

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

root@ub-k8s-master:~# curl -k https://secured.bethel.local --resolve secured.bethel.local:443:192.168.52.19  -i
HTTP/2 501
content-length: 18
content-type: text/plain
date: Sat, 30 Apr 2022 11:41:04 GMT
server: istio-envoy

fault filter abortroot@ub-k8s-master:~# curl -k https://secured.bethel.local --resolve secured.bethel.local:443:192.168.52.19  -i
HTTP/2 200
date: Sat, 30 Apr 2022 11:41:06 GMT
server: istio-envoy
last-modified: Sat, 30 Apr 2022 07:06:17 GMT
etag: "4d-5ddd9cb995b1b"
accept-ranges: bytes
content-length: 77
content-type: text/html
x-envoy-upstream-service-time: 0


```

#### Fault with match

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
      weight: 100
    fault:
      abort:
        httpStatus: 501
        percentage:
          value: 100.0
    match:
    - headers:
        token:
          exact: "PLAIN"
  - route:
    - destination:
        host: https-app-dep-svc
      weight: 100
    match:
    - headers:
        token:
          exact: "TLS"


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
HTTP/2 501
content-length: 18
content-type: text/plain
date: Sat, 30 Apr 2022 11:47:42 GMT
server: istio-envoy

fault filter abortroot@ub-k8s-master:~# curl -k https://secured.bethel.local --resolve secured.bethel.local:443:192.168.52.19  -H "token: PLAIN" -i
HTTP/2 501
content-length: 18
content-type: text/plain
date: Sat, 30 Apr 2022 11:47:45 GMT
server: istio-envoy

fault filter abortroot@ub-k8s-master:~# curl -k https://secured.bethel.local --resolve secured.bethel.local:443:192.168.52.19  -H "token: TLS" -i
HTTP/2 200
date: Sat, 30 Apr 2022 11:47:50 GMT
server: istio-envoy
last-modified: Sat, 30 Apr 2022 07:05:46 GMT
etag: "4f-5ddd9c9c397cd"
accept-ranges: bytes
content-length: 79
content-type: text/html
x-envoy-upstream-service-time: 6

Server : https-app-dep1-5d4b459b4d-6x2nz on Deployment : app-https-app-dep1-v1
root@ub-k8s-master:~# curl -k https://secured.bethel.local --resolve secured.bethel.local:443:192.168.52.19  -H "token: TLS" -i
HTTP/2 200
date: Sat, 30 Apr 2022 11:47:51 GMT
server: istio-envoy
last-modified: Sat, 30 Apr 2022 07:06:00 GMT
etag: "4f-5ddd9ca9d4269"
accept-ranges: bytes
content-length: 79
content-type: text/html
x-envoy-upstream-service-time: 7

Server : https-app-dep2-86b8686db7-7wh7c on Deployment : app-https-app-dep2-v2

```