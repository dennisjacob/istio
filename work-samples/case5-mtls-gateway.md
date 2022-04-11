### mTLS Gateway to http service

```
root@ub-k8s-master:~# kubectl create secret generic gw-case5-mtls-secret --from-file=tls.key=bethel.key --from-file=tls.crt=bethel.crt --from-file=ca.crt=ca.pem -n istio-system
secret/gw-case5-mtls-secret created

root@ub-k8s-master:~# kubectl get secret/gw-case5-mtls-secret -n istio-system
NAME                   TYPE     DATA   AGE
gw-case5-mtls-secret   Opaque   3      2m28s
root@ub-k8s-master:~#

root@ub-k8s-master:~# kubectl get svc
NAME                TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
http-app-dep-svc    ClusterIP   10.109.236.219   <none>        8080/TCP   2d5h
https-app-dep-svc   ClusterIP   10.109.106.185   <none>        8443/TCP   2d5h
root@ub-k8s-master:~#

root@ub-k8s-master:~# cat  case5.yaml
apiVersion: networking.istio.io/v1beta1
kind: Gateway
metadata:
  name: gw-case5
  namespace: my-ns
spec:
  selector:
    istio: ingressgateway
  servers:
  - hosts:
    - api.bethel.local
    port:
      name: https-port
      number: 443
      protocol: HTTPS
    tls:
      mode: MUTUAL
      credentialName: gw-case5-mtls-secret
---
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: vs-case5
  namespace: my-ns
spec:
  hosts:
  - api.bethel.local
  gateways:
  - gw-case5
  http:
  - match:
    - uri:
        prefix: "/"
    route:
    - destination:
        host: http-app-dep-svc
        port:
          number: 8080
      weight: 100    # Optional
      
  
root@ub-k8s-master:~# kubectl apply -f case5.yaml
gateway.networking.istio.io/gw-case5 created
virtualservice.networking.istio.io/vs-case5 created

root@ub-k8s-master:~# curl  https://api.bethel.local:443 --resolve api.bethel.local:443:192.168.52.17 --cacert ./ca.pem  --key ./bethel.key --cert ./bethel.crt
Server : http-app-dep2-6cd478bb5c-ptfdf on Deployment : app-http-app-dep2-v2
root@ub-k8s-master:~# curl  https://api.bethel.local:443 --resolve api.bethel.local:443:192.168.52.17 --cacert ./ca.pem  --key ./bethel.key --cert ./bethel.crt
Server : http-app-dep2-6cd478bb5c-ptfdf on Deployment : app-http-app-dep2-v2
root@ub-k8s-master:~# curl  https://api.bethel.local:443 --resolve api.bethel.local:443:192.168.52.17 --cacert ./ca.pem  --key ./bethel.key --cert ./bethel.crt
Server : http-app-dep1-697974ffdd-nrz2x on Deployment : app-http-app-dep1-v1
root@ub-k8s-master:~# curl  https://api.bethel.local:443 --resolve api.bethel.local:443:192.168.52.17 --cacert ./ca.pem  --key ./bethel.key --cert ./bethel.crt
Server : http-app-dep2-6cd478bb5c-ptfdf on Deployment : app-http-app-dep2-v2


```

### mTLS Gateway to TLS Service

```
root@ub-k8s-master:~# kubectl get svc
NAME                TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
http-app-dep-svc    ClusterIP   10.109.236.219   <none>        8080/TCP   2d5h
https-app-dep-svc   ClusterIP   10.109.106.185   <none>        8443/TCP   2d5h

root@ub-k8s-master:~# cat case5.yaml
apiVersion: networking.istio.io/v1beta1
kind: Gateway
metadata:
  name: gw-case5
  namespace: my-ns
spec:
  selector:
    istio: ingressgateway
  servers:
  - hosts:
    - api.bethel.local
    port:
      name: https-port
      number: 443
      protocol: HTTPS
    tls:
      mode: MUTUAL
      credentialName: gw-case5-mtls-secret
---
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: vs-case5
  namespace: my-ns
spec:
  hosts:
  - api.bethel.local
  gateways:
  - gw-case5
  http:
  - match:
    - uri:
        prefix: "/"
    route:
    - destination:
        host: https-app-dep-svc
        port:
          number: 8443
      weight: 100
---
apiVersion: networking.istio.io/v1beta1
kind: DestinationRule
metadata:
  name: dr-case5
  namespace: my-ns
spec:
  host: https-app-dep-svc
  trafficPolicy:
    tls:
      mode: SIMPLE


root@ub-k8s-master:~# curl  https://api.bethel.local:443 --resolve api.bethel.local:443:192.168.52.17 --cacert ./ca.pem  --key ./bethel.key --cert ./bethel.crt  -i
HTTP/2 200
date: Mon, 11 Apr 2022 11:41:05 GMT
server: istio-envoy
last-modified: Sat, 09 Apr 2022 06:31:34 GMT
etag: "4f-5dc32dcc6e10f"
accept-ranges: bytes
content-length: 79
content-type: text/html
x-envoy-upstream-service-time: 13
Server : https-app-dep2-86b8686db7-8rjwd on Deployment : app-https-app-dep2-v2

```

