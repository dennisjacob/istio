### GetMesh - https://istio.tetratelabs.io/
- Helps providing Istio that is certified against k8s distributions
- GetMesh provides two flavours
  - tetrate distro
  - tetrate fips

### Installation:

```
root@ub-k8s-master:~# curl -sL https://istio.tetratelabs.io/getmesh/install.sh | bash
tetratelabs/getmesh info checking GitHub for latest tag
tetratelabs/getmesh info found version: 1.1.3 for v1.1.3/linux/amd64
tetratelabs/getmesh info installed /root/.getmesh/bin/getmesh
tetratelabs/getmesh info updating user profile (/root/.bashrc)...
tetratelabs/getmesh info the following two lines are added into your profile (/root/.bashrc):
export GETMESH_HOME="$HOME/.getmesh"
export PATH="$GETMESH_HOME/bin:$PATH"
Finished installation. Open a new terminal to start using getmesh!


root@ub-k8s-master:~# export GETMESH_HOME="$HOME/.getmesh"
root@ub-k8s-master:~# export PATH="$GETMESH_HOME/bin:$PATH"
root@ub-k8s-master:~# getmesh version
getmesh version: 1.1.3
active istioctl: 1.11.3-tetrate-v0
no running Istio pods in "istio-system"
1.11.3-tetrate-v0



root@ub-k8s-master:~# getmesh istioctl install --set profile=demo
✔ No issues found when checking the cluster. Istio is safe to install or upgrade!
  To get started, check out https://istio.io/latest/docs/setup/getting-started/

This will install the Istio 1.11.3 demo profile with ["Istio core" "Istiod" "Ingress gateways" "Egress gateways"] components into the cluster. Proceed? (y/N) y
✔ Istio core installed
✔ Istiod installed
✔ Egress gateways installed
✔ Ingress gateways installed
✔ Installation complete
Thank you for installing Istio 1.11.  Please take a few minutes to tell us about your install/upgrade experience!  https://forms.gle/kWULBRjUv7hHci7T6
1 Istio control planes detected, checking --revision "default" only
✔ Deployment: istio-egressgateway.istio-system checked successfully
✔ PodDisruptionBudget: istio-egressgateway.istio-system checked successfully
✔ Role: istio-egressgateway-sds.istio-system checked successfully
✔ RoleBinding: istio-egressgateway-sds.istio-system checked successfully
✔ Service: istio-egressgateway.istio-system checked successfully
✔ ServiceAccount: istio-egressgateway-service-account.istio-system checked successfully
✔ ClusterRole: istiod-istio-system.istio-system checked successfully
✔ ClusterRole: istio-reader-istio-system.istio-system checked successfully
✔ ClusterRoleBinding: istio-reader-istio-system.istio-system checked successfully
✔ ClusterRoleBinding: istiod-istio-system.istio-system checked successfully
✔ ServiceAccount: istio-reader-service-account.istio-system checked successfully
✔ Role: istiod-istio-system.istio-system checked successfully
✔ RoleBinding: istiod-istio-system.istio-system checked successfully
✔ ServiceAccount: istiod-service-account.istio-system checked successfully
✔ CustomResourceDefinition: destinationrules.networking.istio.io.istio-system checked successfully
✔ CustomResourceDefinition: envoyfilters.networking.istio.io.istio-system checked successfully
✔ CustomResourceDefinition: gateways.networking.istio.io.istio-system checked successfully
✔ CustomResourceDefinition: serviceentries.networking.istio.io.istio-system checked successfully
✔ CustomResourceDefinition: sidecars.networking.istio.io.istio-system checked successfully
✔ CustomResourceDefinition: virtualservices.networking.istio.io.istio-system checked successfully
✔ CustomResourceDefinition: workloadentries.networking.istio.io.istio-system checked successfully
✔ CustomResourceDefinition: workloadgroups.networking.istio.io.istio-system checked successfully
✔ CustomResourceDefinition: authorizationpolicies.security.istio.io.istio-system checked successfully
✔ CustomResourceDefinition: peerauthentications.security.istio.io.istio-system checked successfully
✔ CustomResourceDefinition: requestauthentications.security.istio.io.istio-system checked successfully
✔ CustomResourceDefinition: telemetries.telemetry.istio.io.istio-system checked successfully
✔ CustomResourceDefinition: istiooperators.install.istio.io.istio-system checked successfully
✔ ClusterRole: istiod-clusterrole-istio-system.istio-system checked successfully
✔ ClusterRoleBinding: istiod-clusterrole-istio-system.istio-system checked successfully
✔ ConfigMap: istio.istio-system checked successfully
✔ Deployment: istiod.istio-system checked successfully
✔ ConfigMap: istio-sidecar-injector.istio-system checked successfully
✔ MutatingWebhookConfiguration: istio-sidecar-injector.istio-system checked successfully
✔ PodDisruptionBudget: istiod.istio-system checked successfully
✔ ClusterRole: istio-reader-clusterrole-istio-system.istio-system checked successfully
✔ ClusterRoleBinding: istio-reader-clusterrole-istio-system.istio-system checked successfully
✔ Role: istiod.istio-system checked successfully
✔ RoleBinding: istiod.istio-system checked successfully
✔ Service: istiod.istio-system checked successfully
✔ ServiceAccount: istiod.istio-system checked successfully
✔ EnvoyFilter: metadata-exchange-1.10.istio-system checked successfully
✔ EnvoyFilter: tcp-metadata-exchange-1.10.istio-system checked successfully
✔ EnvoyFilter: stats-filter-1.10.istio-system checked successfully
✔ EnvoyFilter: tcp-stats-filter-1.10.istio-system checked successfully
✔ EnvoyFilter: metadata-exchange-1.11.istio-system checked successfully
✔ EnvoyFilter: tcp-metadata-exchange-1.11.istio-system checked successfully
✔ EnvoyFilter: stats-filter-1.11.istio-system checked successfully
✔ EnvoyFilter: tcp-stats-filter-1.11.istio-system checked successfully
✔ EnvoyFilter: metadata-exchange-1.9.istio-system checked successfully
✔ EnvoyFilter: tcp-metadata-exchange-1.9.istio-system checked successfully
✔ EnvoyFilter: stats-filter-1.9.istio-system checked successfully
✔ EnvoyFilter: tcp-stats-filter-1.9.istio-system checked successfully
✔ ValidatingWebhookConfiguration: istio-validator-istio-system.istio-system checked successfully
✔ Deployment: istio-ingressgateway.istio-system checked successfully
✔ PodDisruptionBudget: istio-ingressgateway.istio-system checked successfully
✔ Role: istio-ingressgateway-sds.istio-system checked successfully
✔ RoleBinding: istio-ingressgateway-sds.istio-system checked successfully
✔ Service: istio-ingressgateway.istio-system checked successfully
✔ ServiceAccount: istio-ingressgateway-service-account.istio-system checked successfully
Checked 13 custom resource definitions
Checked 3 Istio Deployments
✔ Istio is installed and verified successfully
root@ub-k8s-master:~#



root@ub-k8s-master:~# getmesh version
getmesh version: 1.1.3
active istioctl: 1.11.3-tetrate-v0
client version: 1.11.3-tetrate-v0
control plane version: 1.11.3-tetrate-v0
data plane version: 1.11.3-tetrate-v0 (2 proxies)
root@ub-k8s-master:~#


root@ub-k8s-master:~# getmesh config-validate
Running the config validator. This may take some time...

2022-02-15T13:35:28.307077Z     info    klog    batch/v1beta1 CronJob is deprecated in v1.21+, unavailable in v1.25+; use batch/v1 CronJob[]
2022-02-15T13:35:28.309675Z     info    klog    batch/v1beta1 CronJob is deprecated in v1.21+, unavailable in v1.25+; use batch/v1 CronJob[]
2022-02-15T13:35:29.088334Z     info    klog    batch/v1beta1 CronJob is deprecated in v1.21+, unavailable in v1.25+; use batch/v1 CronJob[]
2022-02-15T13:35:29.688702Z     info    klog    batch/v1beta1 CronJob is deprecated in v1.21+, unavailable in v1.25+; use batch/v1 CronJob[]
2022-02-15T13:35:30Z WRN Workload speaker of type DaemonSet has not a ReplicaSet as a child controller, it may need a revisit
2022-02-15T13:35:31.285759Z     info    klog    Throttling request took 1.000293728s, request: GET:https://192.168.13.133:6443/api/v1/namespaces/istio-system/configmaps/istio[]
2022-02-15T13:35:31.690041Z     info    klog    batch/v1beta1 CronJob is deprecated in v1.21+, unavailable in v1.25+; use batch/v1 CronJob[]
2022-02-15T13:35:31.887380Z     info    klog    batch/v1beta1 CronJob is deprecated in v1.21+, unavailable in v1.25+; use batch/v1 CronJob[]
2022-02-15T13:35:32.887213Z     info    klog    batch/v1beta1 CronJob is deprecated in v1.21+, unavailable in v1.25+; use batch/v1 CronJob[]
2022-02-15T13:35:33.487734Z     info    klog    batch/v1beta1 CronJob is deprecated in v1.21+, unavailable in v1.25+; use batch/v1 CronJob[]
2022-02-15T13:35:33Z WRN Workload speaker of type DaemonSet has not a ReplicaSet as a child controller, it may need a revisit
2022-02-15T13:35:35.289274Z     info    klog    batch/v1beta1 CronJob is deprecated in v1.21+, unavailable in v1.25+; use batch/v1 CronJob[]
2022-02-15T13:35:35.688462Z     info    klog    batch/v1beta1 CronJob is deprecated in v1.21+, unavailable in v1.25+; use batch/v1 CronJob[]
2022-02-15T13:35:36Z WRN Workload speaker of type DaemonSet has not a ReplicaSet as a child controller, it may need a revisit
2022-02-15T13:35:36.686664Z     info    klog    batch/v1beta1 CronJob is deprecated in v1.21+, unavailable in v1.25+; use batch/v1 CronJob[]
2022-02-15T13:35:37.288212Z     info    klog    batch/v1beta1 CronJob is deprecated in v1.21+, unavailable in v1.25+; use batch/v1 CronJob[]
2022-02-15T13:35:37Z WRN Workload speaker of type DaemonSet has not a ReplicaSet as a child controller, it may need a revisit
NAMESPACE       NAME            RESOURCE TYPE   ERROR CODE      SEVERITY        MESSAGE
default         default         Namespace       IST0102         Info            The namespace is not enabled for Istio injection. Run 'kubectl label namespace default istio-injection=enabled' to
                                                                                enable it, or 'kubectl label namespace default istio-injection=disabled' to explicitly mark it as not needing injection.
istio-system    istio-system    Namespace       IST0102         Info            The namespace is not enabled for Istio injection. Run 'kubectl label namespace istio-system istio-injection=enabled'
                                                                                to enable it, or 'kubectl label namespace istio-system istio-injection=disabled' to explicitly mark it as not needing
                                                                                injection.
metallb-system  metallb-system  Namespace       IST0102         Info            The namespace is not enabled for Istio injection. Run 'kubectl label namespace metallb-system istio-injection=enabled'
                                                                                to enable it, or 'kubectl label namespace metallb-system istio-injection=disabled' to explicitly mark it as not needing
                                                                                injection.
default         hello-world     Service         IST0118         Info            Port name  (port: 8000, targetPort: 8000) doesn't follow the naming convention of Istio port.

The error codes of the found issues are prefixed by 'IST' or 'KIA'. For the detailed explanation, please refer to
- https://istio.io/latest/docs/reference/config/analysis/ for 'IST' error codes
- https://kiali.io/documentation/latest/validations/ for 'KIA' error codes
root@ub-k8s-master:~#



root@ub-k8s-master:~# getmesh show
1.11.3-tetrate-v0 (Active)
root@ub-k8s-master:~# getmesh list
ISTIO VERSION     FLAVOR        FLAVOR VERSION     K8S VERSIONS
   *1.11.3        tetrate             0         1.17,1.18,1.19,1.20
   1.11.3       tetratefips           0         1.17,1.18,1.19,1.20
   1.11.3          istio              0         1.17,1.18,1.19,1.20
   1.10.3         tetrate             0         1.17,1.18,1.19,1.20
   1.10.3       tetratefips           0         1.17,1.18,1.19,1.20
   1.10.3          istio              0         1.17,1.18,1.19,1.20
    1.9.7         tetrate             0         1.17,1.18,1.19,1.20
    1.9.7       tetratefips           0         1.17,1.18,1.19,1.20
    1.9.7          istio              0         1.17,1.18,1.19,1.20
    1.9.5         tetrate             0         1.17,1.18,1.19,1.20
    1.9.5          istio              0         1.17,1.18,1.19,1.20
    1.9.4         tetrate             0         1.17,1.18,1.19,1.20
    1.9.4          istio              0         1.17,1.18,1.19,1.20
    1.9.0         tetrate             0         1.17,1.18,1.19,1.20
    1.9.0       tetratefips           1         1.17,1.18,1.19,1.20
    1.9.0          istio              0         1.17,1.18,1.19,1.20
    1.8.6         tetrate             0         1.16,1.17,1.18,1.19
    1.8.6          istio              0         1.16,1.17,1.18,1.19
    1.8.5         tetrate             0         1.16,1.17,1.18,1.19
    1.8.5          istio              0         1.16,1.17,1.18,1.19
    1.8.3         tetrate             0         1.16,1.17,1.18,1.19
    1.8.3       tetratefips           1         1.16,1.17,1.18,1.19
    1.8.3          istio              0         1.16,1.17,1.18,1.19
    1.7.8         tetrate             0           1.16,1.17,1.18
    1.7.8          istio              0           1.16,1.17,1.18
root@ub-k8s-master:~#


root@ub-k8s-master:~# getmesh fetch --version 1.11.3 --flavor istio --flavor-version 0
For more information about 1.11.3-istio-v0, please refer to the release notes:
- https://istio.io/latest/news/releases/1.11.x/announcing-1.11.3/
istioctl switched to 1.11.3-istio-v0 now
root@ub-k8s-master:~#
root@ub-k8s-master:~# getmesh show
1.11.3-istio-v0 (Active)
1.11.3-tetrate-v0

root@ub-k8s-master:~# getmesh switch --name 1.11.3-tetrate-v0
istioctl switched to 1.11.3-tetrate-v0 now
root@ub-k8s-master:~# getmesh show
1.11.3-istio-v0
1.11.3-tetrate-v0 (Active)
root@ub-k8s-master:~#
```
