Istio generate three types of telemetry
- Metrices
  - based on 4 golden signals
    - Latency
    - Traffic
    - Errors
    - Saturation
  - Metrices are collected at 
    - Proxy-Level  ( also can be viewed from /stats endpoint on every Envoy Proxy)
    - Service Level ( cover the 4 golden signals)
      - Capture metrices on HTTP, HTTP/2, gRPC ( https://istio.io/latest/docs/reference/config/metrics/ )
        - Request Count
        - Request Duration
        - Request Size
        - Rsponse Size
        - gRPC Request Message Count
        - gRPC Response Message Count
      - For TCP traffic ( https://istio.io/latest/docs/reference/config/metrics/ )
        - TCP bytes sent
        - TCP bytes received
        - TCP connections opened
        - TCP Connections closed
    - Control plane level ( https://istio.io/latest/docs/reference/commands/pilot-discovery/#metrics )
      - Number of conflicting inbound/outbound listeners
      - Number of clusters without instances
      - Rejected or Ignored configurations etc.
- Distributed traces
- Access Logs


#### Prometheus Installation

```
root@ub-k8s-master:~# kubectl apply -f https://raw.githubusercontent.com/istio/istio/release-1.13/samples/addons/prometheus.yaml
serviceaccount/prometheus created
configmap/prometheus created
clusterrole.rbac.authorization.k8s.io/prometheus created
clusterrolebinding.rbac.authorization.k8s.io/prometheus created
service/prometheus created
deployment.apps/prometheus created
root@ub-k8s-master:~#

root@ub-k8s-master:~# getmesh istioctl dashboard prometheus
http://localhost:9090
Failed to open browser; open http://localhost:9090 in your browser.

```

#### Graffana Installation

```
root@ub-k8s-master:~# kubectl apply -f https://raw.githubusercontent.com/istio/istio/release-1.13/samples/addons/grafana.yaml
serviceaccount/grafana created
configmap/grafana created
service/grafana created
deployment.apps/grafana created
configmap/istio-grafana-dashboards created
configmap/istio-services-grafana-dashboards created
root@ub-k8s-master:~#

root@ub-k8s-master:~#  getmesh istioctl dashboard grafana
http://localhost:3000
Failed to open browser; open http://localhost:3000 in your browser.


```
