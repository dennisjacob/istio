### Traffic mirroring

```
apiVersion: networking.istio.io/v1beta1
kind: Gateway
metadata:
  name: gw-case6
  namespace: my-ns
spec:
  selector:
    istio: ingressgateway
  servers:
  - hosts:
    - portal.bethel.local
    port:
      name: https-port
      number: 443
      protocol: HTTPS
    tls:
      mode: SIMPLE
      credentialName: gw-secret-tls
---
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: vs-case6
  namespace: my-ns
spec:
  hosts:
  - portal.bethel.local
  gateways:
  - gw-case6
  http:
  - route:
    - destination:
        host: https-app-dep-svc
        subset: version-v1
    mirror:
      host: https-app-dep-svc
      subset: version-v2
---
apiVersion: networking.istio.io/v1beta1
kind: DestinationRule
metadata:
  name: dr-case6
  namespace: my-ns
spec:
  host: https-app-dep-svc
  trafficPolicy:
    tls:
      mode: SIMPLE
  subsets:
  - name: version-v1
    labels:
      app: https-app-dep1
  - name: version-v2
    labels:
      app: https-app-dep2

```
