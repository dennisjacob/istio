### Gateway receives HTTPS , and send to HTTPS service
--- HTTPS traffic ---> GW ---HTTPS traffic --> services

```
root@ub-k8s-master:~# kubectl get svc
NAME                TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
http-app-dep-svc    ClusterIP   10.109.236.219   <none>        8080/TCP   3h16m
https-app-dep-svc   ClusterIP   10.109.106.185   <none>        8443/TCP   3h8m

root@ub-k8s-master:~# kubectl get secret/gw-secret-tls -n istio-system
NAME            TYPE                DATA   AGE
gw-secret-tls   kubernetes.io/tls   2      102m
root@ub-k8s-master:~#


root@ub-k8s-master:~# cat case3.yaml
apiVersion: networking.istio.io/v1beta1
kind: Gateway
metadata:
  name: gw-case3
  namespace: my-ns
spec:
  selector:
    istio: ingressgateway
  servers:
  - hosts:
    - "portal.bethel.local"
    port:
      name: https-case3
      number: 443
      protocol: HTTPS
    tls:
      mode: SIMPLE
      credentialName: gw-secret-tls
---
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: vs-case3
  namespace: my-ns
spec:
  hosts:
  - "portal.bethel.local"
  gateways:
  - gw-case3
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
  name: dr-case3
  namespace: my-ns
spec:
  host: https-app-dep-svc
  trafficPolicy:
    tls:
      mode: SIMPLE


root@ub-k8s-master:~# kubectl apply -f case3.yaml
gateway.networking.istio.io/gw-case3 unchanged
virtualservice.networking.istio.io/vs-case3 unchanged
destinationrule.networking.istio.io/dr-case3 created

root@ub-k8s-master:~# istioctl proxy-config routes deploy/istio-ingressgateway.istio-system | grep my-ns
http.8080                                secured.bethel.local                       /*                     vs-case1.my-ns
https.443.https443.gw-case2.my-ns        secured.bethel.local, api.bethel.local     /*                     vs-case2.my-ns
https.443.https-case3.gw-case3.my-ns     portal.bethel.local                        /*                     vs-case3.my-ns

root@ub-k8s-master:~# curl https://portal.bethel.local --resolve portal.bethel.local:443:192.168.52.17  --cacert /root/bethel-ca.crt
Server : https-app-dep1-5d4b459b4d-xw9tv on Deployment : app-https-app-dep1-v1
root@ub-k8s-master:~# curl https://portal.bethel.local --resolve portal.bethel.local:443:192.168.52.17  --cacert /root/bethel-ca.crt
Server : https-app-dep1-5d4b459b4d-xw9tv on Deployment : app-https-app-dep1-v1
root@ub-k8s-master:~# curl https://portal.bethel.local --resolve portal.bethel.local:443:192.168.52.17  --cacert /root/bethel-ca.crt
Server : https-app-dep2-86b8686db7-8rjwd on Deployment : app-https-app-dep2-v2
root@ub-k8s-master:~# curl https://portal.bethel.local --resolve portal.bethel.local:443:192.168.52.17  --cacert /root/bethel-ca.crt
Server : https-app-dep1-5d4b459b4d-xw9tv on Deployment : app-https-app-dep1-v1
root@ub-k8s-master:~#

root@ub-k8s-master:~# curl https://api.bethel.local --resolve api.bethel.local:443:192.168.52.17  --cacert /root/bethel-ca.crt -i
HTTP/2 404
date: Sat, 09 Apr 2022 09:46:17 GMT
server: istio-envoy



```
