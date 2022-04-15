### Weighted subset based routing

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
        subset: version-v2
      weight: 20
    - destination:
        host: https-app-dep-svc
        subset: version-v1
      weight: 80
    match:
    - headers:
        test-header:
          exact: "v1"
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


root@ub-k8s-master:~# while true; do curl  https://portal.bethel.local:443 --resolve portal.bethel.local:443:192.168.52.17   --cacert ./ca.pem -H "test-header:  v1"; sleep 1; done
Server : https-app-dep1-5d4b459b4d-xw9tv on Deployment : app-https-app-dep1-v1
Server : https-app-dep1-5d4b459b4d-xw9tv on Deployment : app-https-app-dep1-v1
Server : https-app-dep1-5d4b459b4d-xw9tv on Deployment : app-https-app-dep1-v1
Server : https-app-dep1-5d4b459b4d-xw9tv on Deployment : app-https-app-dep1-v1
Server : https-app-dep1-5d4b459b4d-xw9tv on Deployment : app-https-app-dep1-v1
Server : https-app-dep1-5d4b459b4d-xw9tv on Deployment : app-https-app-dep1-v1
Server : https-app-dep1-5d4b459b4d-xw9tv on Deployment : app-https-app-dep1-v1
Server : https-app-dep1-5d4b459b4d-xw9tv on Deployment : app-https-app-dep1-v1
Server : https-app-dep1-5d4b459b4d-xw9tv on Deployment : app-https-app-dep1-v1
Server : https-app-dep1-5d4b459b4d-xw9tv on Deployment : app-https-app-dep1-v1
Server : https-app-dep1-5d4b459b4d-xw9tv on Deployment : app-https-app-dep1-v1
Server : https-app-dep2-86b8686db7-8rjwd on Deployment : app-https-app-dep2-v2
Server : https-app-dep1-5d4b459b4d-xw9tv on Deployment : app-https-app-dep1-v1
Server : https-app-dep2-86b8686db7-8rjwd on Deployment : app-https-app-dep2-v2
^C

```


