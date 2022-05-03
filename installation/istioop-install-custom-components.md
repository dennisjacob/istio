```
apiVersion: install.istio.io/v1alpha1
kind: IstioOperator
metadata:
  name: my-iop
  namespace: istio-system
spec:
  components:
    base:
      enabled: true
    cni:
      enabled: false
    egressGateways:
    - enabled: true
      name: istio-egressgw
    ingressGateways:
    - enabled: true
      name: istio-ingressgw
    istiodRemote:
      enabled: false
    pilot:
      enabled: true
  meshConfig:
    accessLogFile: /dev/stdout
    outboundTrafficPolicy:
      mode: ALLOW_ANY   #By default
  profile: empty
  tag: 1.13.2



root@ub-k8s-master:~# kubectl get iop,deploy,po -n istio-system
NAME                                    REVISION   STATUS    AGE
istiooperator.install.istio.io/my-iop   1-13-2     HEALTHY   80s

NAME                              READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/istio-egressgw    1/1     1            1           72s
deployment.apps/istio-ingressgw   1/1     1            1           72s
deployment.apps/istiod-1-13-2     1/1     1            1           76s

NAME                                   READY   STATUS    RESTARTS   AGE
pod/istio-egressgw-5bc8b9cb6c-67rt6    1/1     Running   0          72s
pod/istio-ingressgw-84f76bc766-bxx5x   1/1     Running   0          72s
pod/istiod-1-13-2-67b8bf75f8-hzn6l     1/1     Running   0          25s


root@ub-k8s-master:~# kubectl logs po/istio-operator-67b876d65f-krdtk -n istio-operator

2022-05-02T04:31:19.822547Z     error   analysis        error setting up error handling for kube arbitraryclient: 2 errors occurred:
        * informer has already started
        * informer has already started
2022-05-02T04:31:19.822702Z     info    kube    Starting Pilot K8S CRD controller
2022-05-02T04:31:19.822835Z     info    kube    Pilot K8S CRD controller synced 138.273µs
2022-05-02T04:31:19.925832Z     info    installer       IngressGateways is waiting on dependency...
2022-05-02T04:31:19.925906Z     info    installer       Processing resources from manifest: IstiodRemote for CR my-iop-istio-system-IstiodRemote-https://10.96.0.1:443
2022-05-02T04:31:19.925927Z     info    installer       Generated manifest objects are the same as cached for component IstiodRemote.
2022-05-02T04:31:19.925938Z     info    installer       Pilot is waiting on dependency...
2022-05-02T04:31:19.925945Z     info    installer       EgressGateways is waiting on dependency...
2022-05-02T04:31:19.926006Z     info    installer       Processing resources from manifest: Base for CR my-iop-istio-system-Base-https://10.96.0.1:443
2022-05-02T04:31:19.925832Z     info    installer       Cni is waiting on dependency...
2022-05-02T04:31:20.216301Z     info    installer       Generated manifest objects are the same as cached for component Base.
2022-05-02T04:31:20.216474Z     info    installer       Unblocking dependency Pilot.
2022-05-02T04:31:20.216562Z     info    installer       Dependency for Pilot has completed, proceeding.
2022-05-02T04:31:20.216595Z     info    installer       Processing resources from manifest: Pilot for CR my-iop-istio-system-Pilot-https://10.96.0.1:443
2022-05-02T04:31:20.320636Z     info    installer       Generated manifest objects are the same as cached for component Pilot.
2022-05-02T04:31:20.320674Z     info    installer       Unblocking dependency Cni.
2022-05-02T04:31:20.320691Z     info    installer       Unblocking dependency IngressGateways.
2022-05-02T04:31:20.320695Z     info    installer       Unblocking dependency EgressGateways.
2022-05-02T04:31:20.320709Z     info    installer       Dependency for Cni has completed, proceeding.
2022-05-02T04:31:20.320766Z     info    installer       Processing resources from manifest: Cni for CR my-iop-istio-system-Cni-https://10.96.0.1:443
2022-05-02T04:31:20.320779Z     info    installer       Generated manifest objects are the same as cached for component Cni.
2022-05-02T04:31:20.320728Z     info    installer       Dependency for IngressGateways has completed, proceeding.
2022-05-02T04:31:20.320789Z     info    installer       Processing resources from manifest: IngressGateways for CR my-iop-istio-system-IngressGateways-https://10.96.0.1:443
2022-05-02T04:31:20.320700Z     info    installer       Dependency for EgressGateways has completed, proceeding.
2022-05-02T04:31:20.320866Z     info    installer       Processing resources from manifest: EgressGateways for CR my-iop-istio-system-EgressGateways-https://10.96.0.1:443
2022-05-02T04:31:20.323514Z     info    installer       Generated manifest objects are the same as cached for component EgressGateways.
2022-05-02T04:31:20.412440Z     info    installer       Generated manifest objects are the same as cached for component IngressGateways.
- Pruning removed resources

```