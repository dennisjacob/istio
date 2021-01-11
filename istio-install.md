## Installation of Istio

Reference: https://istio.io/latest/docs/setup/getting-started/#download


### Pre-requisites:
Kubernetes cluster up and running

### Installation steps:

#### Download Istio

```bash

root@k8s-master:~# curl -L https://istio.io/downloadIstio | sh -
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   102  100   102    0     0    140      0 --:--:-- --:--:-- --:--:--   140
100  4579  100  4579    0     0   4132      0  0:00:01  0:00:01 --:--:--  4132

Downloading istio-1.8.1 from https://github.com/istio/istio/releases/download/1.8.1/istio-1.8.1-linux-amd64.tar.gz ...


Istio 1.8.1 Download Complete!

Istio has been successfully downloaded into the istio-1.8.1 folder on your system.

Next Steps:
See https://istio.io/latest/docs/setup/install/ to add Istio to your Kubernetes cluster.

To configure the istioctl client tool for your workstation,
add the /root/istio-1.8.1/bin directory to your environment path variable with:
         export PATH="$PATH:/root/istio-1.8.1/bin"

Begin the Istio pre-installation check by running:
         istioctl x precheck

Need more information? Visit https://istio.io/latest/docs/setup/install/

root@k8s-master:~# cd istio-1.8.1/
root@k8s-master:~/istio-1.8.1# export PATH=$PWD/bin:$PATH
root@k8s-master:~/istio-1.8.1# which istioctl
/root/istio-1.8.1/bin/istioctl


```

#### Perform Istio Pre-check

```bash
root@k8s-master:~/istio-1.8.1# istioctl x precheck

Checking the cluster to make sure it is ready for Istio installation...

#1. Kubernetes-api
-----------------------
Can initialize the Kubernetes client.
Can query the Kubernetes API Server.

#2. Kubernetes-version
-----------------------
Istio is compatible with Kubernetes: v1.20.1.

#3. Istio-existence
-----------------------
Istio will be installed in the istio-system namespace.

#4. Kubernetes-setup
-----------------------
Can create necessary Kubernetes configurations: Namespace,ClusterRole,ClusterRoleBinding,CustomResourceDefinition,Role,ServiceAccount,Service,Deployments,ConfigMap.

#5. SideCar-Injector
-----------------------
This Kubernetes cluster supports automatic sidecar injection. To enable automatic sidecar injection see https://istio.io/v1.8/docs/setup/additional-setup/sidecar-injection/#deploying-an-app

-----------------------
Install Pre-Check passed! The cluster is ready for Istio installation.

```

#### Install the istio.

The following built-in configuration profiles are currently available:
https://istio.io/latest/docs/setup/additional-setup/config-profiles/

```
root@k8s-master:~/istio-1.8.1# istioctl install --set profile=default -y
✔ Istio core installed
✔ Istiod installed
✔ Ingress gateways installed
✔ Installation complete                                                                                                                                                                                       root@k8s-master:~/istio-1.8.1#

```

#### Create/Use a namespace to instruct Istio to automatically inject the Envoy sidecar proxy with every pod you deploy in the my-ns namespace.

```
root@k8s-master:~/istio-1.8.1# kubectl create ns my-ns
namespace/my-ns created
root@k8s-master:~/istio-1.8.1# kubectl config set-context $(kubectl config current-context)  --namespace my-ns
Context "kubernetes-admin@kubernetes" modified.
root@k8s-master:~/istio-1.8.1# kubectl label namespace my-ns istio-injection=enabled
namespace/my-ns labeled
root@k8s-master:~/istio-1.8.1#

```

#### Validate that there are no issues with the configuration.

```
root@k8s-master:~/istio-1.8.1# istioctl analyze

✔ No validation issues found when analyzing namespace: my-ns.

```

#### Deploy the addons - Kiali UI , Prometheus, Grafana, Jaegar

```
root@k8s-master:~/istio-1.8.1# kubectl apply -f samples/addons
serviceaccount/grafana unchanged
configmap/grafana unchanged
service/grafana unchanged
deployment.apps/grafana configured
configmap/istio-grafana-dashboards configured
configmap/istio-services-grafana-dashboards configured
deployment.apps/jaeger unchanged
service/tracing unchanged
service/zipkin unchanged
service/jaeger-collector unchanged
Warning: apiextensions.k8s.io/v1beta1 CustomResourceDefinition is deprecated in v1.16+, unavailable in v1.22+; use apiextensions.k8s.io/v1 CustomResourceDefinition
customresourcedefinition.apiextensions.k8s.io/monitoringdashboards.monitoring.kiali.io unchanged
serviceaccount/kiali unchanged
configmap/kiali unchanged
clusterrole.rbac.authorization.k8s.io/kiali-viewer unchanged
clusterrole.rbac.authorization.k8s.io/kiali unchanged
clusterrolebinding.rbac.authorization.k8s.io/kiali unchanged
service/kiali unchanged
deployment.apps/kiali unchanged
monitoringdashboard.monitoring.kiali.io/envoy unchanged
monitoringdashboard.monitoring.kiali.io/go unchanged
monitoringdashboard.monitoring.kiali.io/kiali unchanged
monitoringdashboard.monitoring.kiali.io/micrometer-1.0.6-jvm-pool unchanged
monitoringdashboard.monitoring.kiali.io/micrometer-1.0.6-jvm unchanged
monitoringdashboard.monitoring.kiali.io/micrometer-1.1-jvm unchanged
monitoringdashboard.monitoring.kiali.io/microprofile-1.1 unchanged
monitoringdashboard.monitoring.kiali.io/microprofile-x.y unchanged
monitoringdashboard.monitoring.kiali.io/nodejs unchanged
monitoringdashboard.monitoring.kiali.io/quarkus unchanged
monitoringdashboard.monitoring.kiali.io/springboot-jvm-pool unchanged
monitoringdashboard.monitoring.kiali.io/springboot-jvm unchanged
monitoringdashboard.monitoring.kiali.io/springboot-tomcat unchanged
monitoringdashboard.monitoring.kiali.io/thorntail unchanged
monitoringdashboard.monitoring.kiali.io/tomcat unchanged
monitoringdashboard.monitoring.kiali.io/vertx-client unchanged
monitoringdashboard.monitoring.kiali.io/vertx-eventbus unchanged
monitoringdashboard.monitoring.kiali.io/vertx-jvm unchanged
monitoringdashboard.monitoring.kiali.io/vertx-pool unchanged
monitoringdashboard.monitoring.kiali.io/vertx-server unchanged
serviceaccount/prometheus unchanged
configmap/prometheus unchanged
clusterrole.rbac.authorization.k8s.io/prometheus unchanged
clusterrolebinding.rbac.authorization.k8s.io/prometheus unchanged
service/prometheus unchanged
deployment.apps/prometheus configured
root@k8s-master:~/istio-1.8.1#

```


#### Run the kiali UI

```
root@k8s-master:~/istio-1.8.1# istioctl dashboard kiali
http://localhost:20001/kiali
Failed to open browser; open http://localhost:20001/kiali in your browser.

```

The URL is a localhost URL. You may need to do an SSH tunneling to access the URL

```
root@bethel-ws:~# ssh -L 20001:localhost:20001 dennis@192.168.13.131
dennis@192.168.13.131's password:
Welcome to Ubuntu 20.10 (GNU/Linux 5.8.0-34-generic x86_64)

```
