### Installing Istio
- Using istioctl
    - Generate YAML using ( istioctl manifest generate )
    - Install to k8s ( istioctl install )
    - istioctl can also be used for debugging
    - Istioctl can use Profiles ( each profile has varying components )
      - recommended profile is default ( for Prod )
      - demo
      - minimal ( minimal features)      
- Using Istio Operator

### Download Istio

```
root@ub-k8s-master:~#  curl -L https://istio.io/downloadIstio | ISTIO_VERSION=1.13.2 TARGET_ARCH=x86_64 sh -
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   101  100   101    0     0    885      0 --:--:-- --:--:-- --:--:--   885
100  4926  100  4926    0     0  29147      0 --:--:-- --:--:-- --:--:-- 29147

Downloading istio-1.13.2 from https://github.com/istio/istio/releases/download/1.13.2/istio-1.13.2-linux-amd64.tar.gz ...

Istio 1.13.2 Download Complete!

Istio has been successfully downloaded into the istio-1.13.2 folder on your system.

Next Steps:
See https://istio.io/latest/docs/setup/install/ to add Istio to your Kubernetes cluster.

To configure the istioctl client tool for your workstation,
add the /root/istio-1.13.2/bin directory to your environment path variable with:
         export PATH="$PATH:/root/istio-1.13.2/bin"

Begin the Istio pre-installation check by running:
         istioctl x precheck

Need more information? Visit https://istio.io/latest/docs/setup/install/
root@ub-k8s-master:~# ls -ltr
drwxr-x--- 6 root root 4096 Mar  8 21:41 istio-1.13.2
root@ub-k8s-master:~# export PATH=$PATH:$(pwd)/istio-1.13.2/bin
root@ub-k8s-master:~#  which istioctl
/root/istio-1.13.2/bin/istioctl
root@ub-k8s-master:~#

```

### Istio Operator API Documentation

```
https://istio.io/latest/docs/reference/config/istio.operator.v1alpha1/
```


### Install Istio with istioctl

```
root@ub-k8s-master:~# istioctl install --set profile=default --set meshConfig.enableTracing=true --set meshConfig.accessLogFile=/dev/stdout --dry-run
✔ Istio core installed
✔ Istiod installed
✔ Ingress gateways installed
✔ Installation complete                                                                                                                                                                                        Making this installation the default for injection and validation.
root@ub-k8s-master:~# istioctl install --set profile=default --set meshConfig.enableTracing=true --set meshConfig.accessLogFile=/dev/stdout
This will install the Istio 1.13.2 default profile with ["Istio core" "Istiod" "Ingress gateways"] components into the cluster. Proceed? (y/N) y
✔ Istio core installed
✔ Istiod installed
✔ Ingress gateways installed
✔ Installation complete                                                                                                                                                                                        Making this installation the default for injection and validation.

Thank you for installing Istio 1.13.  Please take a few minutes to tell us about your install/upgrade experience!  https://forms.gle/pzWZpAvMVBecaQ9h9
root@ub-k8s-master:~#


root@ub-k8s-master:~# kubectl -n istio-system get deploy
NAME                   READY   UP-TO-DATE   AVAILABLE   AGE
istio-ingressgateway   1/1     1            1           9m51s
istiod                 1/1     1            1           10m

# View all installed components and their stetings
 kubectl -n istio-system get IstioOperator installed-state -o yaml

```
