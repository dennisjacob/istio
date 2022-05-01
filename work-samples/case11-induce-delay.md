#### Induce a delay

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
      delay:
        fixedDelay: 2s
        percentage:
          value: 100.0


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


```