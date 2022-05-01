
### Passthrough Gateway ( TLS termination on application containers)

```

root@ub-k8s-master:~# cat case4.yaml
apiVersion: networking.istio.io/v1beta1
kind: Gateway
metadata:
  name: gw-case4
  namespace: my-ns
spec:
  selector:
    istio: ingressgateway
  servers:
  - hosts:
    - portal.bethel.local
    port:
      name: https
      number: 443
      protocol: HTTPS
    tls:
      mode: PASSTHROUGH
---
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: vs-case4
  namespace: my-ns
spec:
  hosts:
  - portal.bethel.local
  gateways:
  - gw-case4
  tls:
  - match:
    - port: 443
      sniHosts:
      - portal.bethel.local
    route:
    - destination:
        host: https-app-dep-svc
        port:
          number: 8443
          
# if the Gateway is configured with TLS PASSTHROUGH while the DestinationRule configures TLS origination, you will end up with double encryption. This works, but is often not the desired behavior.

root@ub-k8s-master:~# kubectl apply -f case4.yaml
gateway.networking.istio.io/gw-case4 created
virtualservice.networking.istio.io/vs-case4 created

root@ub-k8s-master:~# kubectl get svc/https-app-dep-svc
NAME                TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
https-app-dep-svc   ClusterIP   10.109.106.185   <none>        8443/TCP   30h

root@ub-k8s-master:~# openssl s_client -connect 10.109.106.185:8443 -servername portal.bethel.local -showcerts 2>/dev/null | openssl x509 -subject -issuer -dates -noout
subject=C = SG, L = Singapore, O = bethel, CN = portal.bethel.local, emailAddress = djacob@gmail.com
issuer=CN = bethel.local
notBefore=Apr  9 05:46:49 2022 GMT
notAfter=Apr  9 05:46:49 2023 GMT


root@ub-k8s-master:~# curl  https://portal.bethel.local:443 --resolve portal.bethel.local:443:192.168.52.17 --cacert ./ca.pem  -v
* Added portal.bethel.local:443:192.168.52.17 to DNS cache
* Hostname portal.bethel.local was found in DNS cache
*   Trying 192.168.52.17:443...
* TCP_NODELAY set
* Connected to portal.bethel.local (192.168.52.17) port 443 (#0)
* ALPN, offering h2
* ALPN, offering http/1.1
* successfully set certificate verify locations:
*   CAfile: ./ca.pem
  CApath: /etc/ssl/certs
* TLSv1.3 (OUT), TLS handshake, Client hello (1):
* TLSv1.3 (IN), TLS handshake, Server hello (2):
* TLSv1.3 (IN), TLS handshake, Encrypted Extensions (8):
* TLSv1.3 (IN), TLS handshake, Certificate (11):
* TLSv1.3 (IN), TLS handshake, CERT verify (15):
* TLSv1.3 (IN), TLS handshake, Finished (20):
* TLSv1.3 (OUT), TLS change cipher, Change cipher spec (1):
* TLSv1.3 (OUT), TLS handshake, Finished (20):
* SSL connection using TLSv1.3 / TLS_AES_256_GCM_SHA384
* ALPN, server accepted to use http/1.1
* Server certificate:
*  subject: C=SG; L=Singapore; O=bethel; CN=portal.bethel.local; emailAddress=djacob@gmail.com
*  start date: Apr  9 05:46:49 2022 GMT
*  expire date: Apr  9 05:46:49 2023 GMT
*  subjectAltName: host "portal.bethel.local" matched cert's "portal.bethel.local"
*  issuer: CN=bethel.local
*  SSL certificate verify ok.
> GET / HTTP/1.1
> Host: portal.bethel.local
> User-Agent: curl/7.68.0
> Accept: */*
>
* TLSv1.3 (IN), TLS handshake, Newsession Ticket (4):
* TLSv1.3 (IN), TLS handshake, Newsession Ticket (4):
* old SSL session ID is stale, removing
* Mark bundle as not supporting multiuse
< HTTP/1.1 200 OK
< Date: Sun, 10 Apr 2022 13:00:19 GMT
< Server: Apache/2.4.53 (Unix) OpenSSL/1.1.1f
< Last-Modified: Sat, 09 Apr 2022 06:31:34 GMT
< ETag: "4f-5dc32dcc6e10f"
< Accept-Ranges: bytes
< Content-Length: 79
< Content-Type: text/html
<
Server : https-app-dep2-86b8686db7-8rjwd on Deployment : app-https-app-dep2-v2
* Connection #0 to host portal.bethel.local left intact
root@ub-k8s-master:~#



```
