### Gateway receives HTTPS , and send to HTTP service

--- HTTPS traffic ---> GW ---HTTP traffic --> services

```
root@ub-k8s-master:~# kubectl create secret tls gw-secret-tls --cert=/root/bethel.crt --key=/root/bethel.key -n istio-system
secret/gw-secret-tls created

root@ub-k8s-master:~# kubectl get secret/gw-secret-tls -n istio-system
NAME            TYPE                DATA   AGE
gw-secret-tls   kubernetes.io/tls   2      15s

root@ub-k8s-master:~# cat case2.yaml
apiVersion: networking.istio.io/v1beta1
kind: Gateway
metadata:
  name: gw-case2
  namespace: my-ns
spec:
  selector:
    istio: ingressgateway
  servers:
  - hosts:
    - "secured.bethel.local"
    - "api.bethel.local"
    port:
      name: https443
      number: 443
      protocol: HTTPS
    tls:
      mode: SIMPLE
      credentialName: gw-secret-tls
---
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: vs-case2
  namespace: my-ns
spec:
  hosts:
  - "secured.bethel.local"
  - "api.bethel.local"
  gateways:
  - gw-case2
  http:
  - match:
    - uri:
        prefix: "/"
    route:
    - destination:
        host: http-app-dep-svc
        port:
          number: 8080


root@ub-k8s-master:~# kubectl apply -f case2.yaml
gateway.networking.istio.io/gw-case2 created
virtualservice.networking.istio.io/vs-case2 created
root@ub-k8s-master:~#

root@ub-k8s-master:~# istioctl proxy-config routes deploy/istio-ingressgateway.istio-system | grep my-ns
http.8080                             secured.bethel.local                       /*                     vs-case1.my-ns
https.443.https443.gw-case2.my-ns     secured.bethel.local, api.bethel.local     /*                     vs-case2.my-ns
root@ub-k8s-master:~#

root@ub-k8s-master:~# kubectl get svc/istio-ingressgateway -n istio-system  -ojsonpath='{.status.loadBalancer.ingress[0].ip} {"\n"}'
192.168.52.17


root@ub-k8s-master:~# curl https://secured.bethel.local --resolve secured.bethel.local:443:192.168.52.17  --cacert /root/bethel-ca.crt
Server : http-app-dep2-6cd478bb5c-ptfdf on Deployment : app-http-app-dep2-v2
root@ub-k8s-master:~# curl https://secured.bethel.local --resolve secured.bethel.local:443:192.168.52.17  --cacert /root/bethel-ca.crt
Server : http-app-dep2-6cd478bb5c-ptfdf on Deployment : app-http-app-dep2-v2
root@ub-k8s-master:~# curl https://secured.bethel.local --resolve secured.bethel.local:443:192.168.52.17  --cacert /root/bethel-ca.crt
Server : http-app-dep1-697974ffdd-nrz2x on Deployment : app-http-app-dep1-v1
root@ub-k8s-master:~# curl https://secured.bethel.local --resolve secured.bethel.local:443:192.168.52.17  --cacert /root/bethel-ca.crt
Server : http-app-dep2-6cd478bb5c-ptfdf on Deployment : app-http-app-dep2-v2
root@ub-k8s-master:~# curl https://secured.bethel.local --resolve secured.bethel.local:443:192.168.52.17  --cacert /root/bethel-ca.crt
Server : http-app-dep1-697974ffdd-nrz2x on Deployment : app-http-app-dep1-v1
root@ub-k8s-master:~#


```
