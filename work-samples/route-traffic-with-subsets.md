### Route the traffic to different deployments using subsets

```
root@ub-k8s-master:~# kubectl get po --show-labels -l run=httpsapp
NAME                              READY   STATUS    RESTARTS       AGE     LABELS
https-app-dep1-5d4b459b4d-xw9tv   1/1     Running   1 (2d5h ago)   5d23h   app=https-app-dep1,pod-template-hash=5d4b459b4d,run=httpsapp
https-app-dep2-86b8686db7-8rjwd   1/1     Running   1 (2d5h ago)   5d23h   app=https-app-dep2,pod-template-hash=86b8686db7,run=httpsapp
root@ub-k8s-master:~#

root@ub-k8s-master:~# cat case6.yaml
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
  - match:
    - uri:
        prefix: "/"
    route:
    - destination:
        host: https-app-dep-svc
        port:
          number: 8443
        subset: version-v1
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


root@ub-k8s-master:~# kubectl apply -f case6.yaml
gateway.networking.istio.io/gw-case6 created
virtualservice.networking.istio.io/vs-case6 created
destinationrule.networking.istio.io/dr-case6 created
root@ub-k8s-master:~#

root@ub-k8s-master:~# curl  https://portal.bethel.local:443 --resolve portal.bethel.local:443:192.168.52.17   --cacert ./ca.pem
Server : https-app-dep1-5d4b459b4d-xw9tv on Deployment : app-https-app-dep1-v1
root@ub-k8s-master:~# curl  https://portal.bethel.local:443 --resolve portal.bethel.local:443:192.168.52.17   --cacert ./ca.pem
Server : https-app-dep1-5d4b459b4d-xw9tv on Deployment : app-https-app-dep1-v1
root@ub-k8s-master:~# curl  https://portal.bethel.local:443 --resolve portal.bethel.local:443:192.168.52.17   --cacert ./ca.pem
Server : https-app-dep1-5d4b459b4d-xw9tv on Deployment : app-https-app-dep1-v1
root@ub-k8s-master:~# curl  https://portal.bethel.local:443 --resolve portal.bethel.local:443:192.168.52.17   --cacert ./ca.pem
Server : https-app-dep1-5d4b459b4d-xw9tv on Deployment : app-https-app-dep1-v1

```

### Modify the virtualservice to point to v2

```
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
  - match:
    - uri:
        prefix: "/"
    route:
    - destination:
        host: https-app-dep-svc
        port:
          number: 8443
        subset: version-v2
        
root@ub-k8s-master:~# kubectl apply -f case6.yaml
gateway.networking.istio.io/gw-case6 unchanged
virtualservice.networking.istio.io/vs-case6 configured
destinationrule.networking.istio.io/dr-case6 unchanged


root@ub-k8s-master:~# curl  https://portal.bethel.local:443 --resolve portal.bethel.local:443:192.168.52.17   --cacert ./ca.pem
Server : https-app-dep2-86b8686db7-8rjwd on Deployment : app-https-app-dep2-v2
root@ub-k8s-master:~# curl  https://portal.bethel.local:443 --resolve portal.bethel.local:443:192.168.52.17   --cacert ./ca.pem
Server : https-app-dep2-86b8686db7-8rjwd on Deployment : app-https-app-dep2-v2
root@ub-k8s-master:~# curl  https://portal.bethel.local:443 --resolve portal.bethel.local:443:192.168.52.17   --cacert ./ca.pem
Server : https-app-dep2-86b8686db7-8rjwd on Deployment : app-https-app-dep2-v2
root@ub-k8s-master:~# curl  https://portal.bethel.local:443 --resolve portal.bethel.local:443:192.168.52.17   --cacert ./ca.pem
Server : https-app-dep2-86b8686db7-8rjwd on Deployment : app-https-app-dep2-v2
root@ub-k8s-master:~# curl  https://portal.bethel.local:443 --resolve portal.bethel.local:443:192.168.52.17   --cacert ./ca.pem
Server : https-app-dep2-86b8686db7-8rjwd on Deployment : app-https-app-dep2-v2
root@ub-k8s-master:~#
       
```
