### Gateway receives HTTP , and send to HTTPS service

--- HTTP traffic ---> GW ---HTTPS traffic --> services

```
apiVersion: networking.istio.io/v1beta1
kind: Gateway
metadata:
  name: gw1
  namespace: my-ns
spec:
  selector:
    istio: ingressgateway
  servers:
  - hosts:
    - "*"
    port:
      name: HTTPX
      number: 80
      protocol: HTTP

---
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: vs1
  namespace: my-ns
spec:
  hosts:
  - "*"
  gateways:
  - gw1
  http:
  - route:
    - destination:
        host: https-app-dep-svc
        port:
          number: 8443
---
apiVersion: networking.istio.io/v1beta1
kind: DestinationRule
metadata:
  name: dr1
  namespace: my-ns
spec:
  host: https-app-dep-svc
  trafficPolicy:
    tls:
      mode: SIMPLE

root@ub-k8s-master:~# curl -k http://secured.bethel.local --resolve secured.bethel.local:80:192.168.52.19
Server : https-app-dep1-5d4b459b4d-6x2nz on Deployment : app-https-app-dep1-v1
root@ub-k8s-master:~# curl -k http://secured.bethel.local --resolve secured.bethel.local:80:192.168.52.19
Server : https-app-dep2-86b8686db7-7wh7c on Deployment : app-https-app-dep2-v2
root@ub-k8s-master:~# curl -k http://secured.bethel.local --resolve secured.bethel.local:80:192.168.52.19
Server : https-app-dep1-5d4b459b4d-6x2nz on Deployment : app-https-app-dep1-v1
```